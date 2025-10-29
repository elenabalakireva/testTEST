@PersistenceContext
    private EntityManager entityManager;
    
    @Transactional
    public void updateFiles() {
        String sql = "UPDATE company_doc_version " +
            "SET files = COALESCE( " +
            "    ( " +
            "        SELECT jsonb_agg( " +
            "            jsonb_set( " +
            "                jsonb_set( " +
            "                    jsonb_set( " +
            "                        jsonb_set( " +
            "                            jsonb_set( " +
            "                                jsonb_set( " +
            "                                    jsonb_set( " +
            "                                        elem, " +
            "                                        '{id}', " +
            "                                        '\"123\"' " +
            "                                    ), " +
            "                                    '{md5}', " +
            "                                    '\"456\"' " +
            "                                ), " +
            "                                '{name}', " +
            "                                '\"test\"' " +
            "                            ), " +
            "                            '{filesize}', " +
            "                            '1' " +
            "                        ), " +
            "                        '{mimeType}', " +
            "                        '\"test\"' " +
            "                    ), " +
            "                    '{extension}', " +
            "                    '\"test\"' " +
            "                ), " +
            "                '{displayName}', " +
            "                '\"test\"' " +
            "            ) " +
            "        ) " +
            "        FROM jsonb_array_elements(files) AS elem " +
            "    ), " +
            "    '[]'::jsonb " +
            ") " +
            "WHERE id BETWEEN 760 AND 761 " +
            "AND files IS NOT NULL";
            
        Query query = entityManager.createNativeQuery(sql);
        int updatedRows = query.executeUpdate();
        System.out.println("Updated rows: " + updatedRows);
    }
