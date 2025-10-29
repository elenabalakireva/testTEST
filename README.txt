@PersistenceContext
    private EntityManager entityManager;
    
    @Transactional
    public void updateFiles(File file) {
        String sql = """
            UPDATE company_doc_version 
            SET files = COALESCE(
                (
                    SELECT jsonb_agg(
                        jsonb_set(
                            jsonb_set(
                                jsonb_set(
                                    jsonb_set(
                                        jsonb_set(
                                            jsonb_set(
                                                jsonb_set(
                                                    elem,
                                                    '{id}',
                                                    CAST(:id AS jsonb)
                                                ),
                                                '{md5}',
                                                CAST(:md5 AS jsonb)
                                            ),
                                            '{name}',
                                            CAST(:name AS jsonb)
                                        ),
                                        '{filesize}',
                                        CAST(:fileSize AS jsonb)
                                    ),
                                    '{mimeType}',
                                    CAST(:mimeType AS jsonb)
                                ),
                                '{extension}',
                                CAST(:extension AS jsonb)
                            ),
                            '{displayName}',
                            CAST(:displayName AS jsonb)
                        )
                    )
                    FROM jsonb_array_elements(files) AS elem
                ),
                '[]'::jsonb
            )
            WHERE id BETWEEN 760 AND 761 
            AND files IS NOT NULL
            """;
            
        Query query = entityManager.createNativeQuery(sql);
        query.setParameter("id", "\"" + file.getId() + "\"");
        query.setParameter("md5", "\"" + file.getMd5() + "\"");
        query.setParameter("name", "\"" + file.getName() + "\"");
        query.setParameter("fileSize", file.getFileSize().toString());
        query.setParameter("mimeType", "\"" + file.getMimeType() + "\"");
        query.setParameter("extension", "\"" + file.getExtension() + "\"");
        query.setParameter("displayName", "\"" + file.getDisplayName() + "\"");
        
        query.executeUpdate();
    }
