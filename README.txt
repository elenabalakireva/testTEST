@PersistenceContext
    private EntityManager entityManager;
    
    @Transactional
    public void updateFiles(File file, Long startId, Long endId) {
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
                                                    ?1
                                                ),
                                                '{md5}',
                                                ?2
                                            ),
                                            '{name}',
                                            ?3
                                        ),
                                        '{filesize}',
                                        ?4
                                    ),
                                    '{mimeType}',
                                    ?5
                                ),
                                '{extension}',
                                ?6
                            ),
                            '{displayName}',
                            ?7
                        )
                    )
                    FROM jsonb_array_elements(files) AS elem
                ),
                '[]'::jsonb
            )
            WHERE id BETWEEN ?8 AND ?9 
            AND files IS NOT NULL
            """;
            
        Query query = entityManager.createNativeQuery(sql);
        query.setParameter(1, "\"" + file.getId() + "\"");
        query.setParameter(2, "\"" + file.getMd5() + "\"");
        query.setParameter(3, "\"" + file.getName() + "\"");
        query.setParameter(4, file.getFileSize().toString());
        query.setParameter(5, "\"" + file.getMimeType() + "\"");
        query.setParameter(6, "\"" + file.getExtension() + "\"");
        query.setParameter(7, "\"" + file.getDisplayName() + "\"");
        query.setParameter(8, startId);
        query.setParameter(9, endId);
        
        query.executeUpdate();
    }
