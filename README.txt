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
            "                                        '\"123\"'::jsonb " +
            "                                    ), " +
            "                                    '{md5}', " +
            "                                    '\"456\"'::jsonb " +
            "                                ), " +
            "                                '{name}', " +
            "                                '\"test\"'::jsonb " +
            "                            ), " +
            "                            '{filesize}', " +
            "                            '1'::jsonb " +
            "                        ), " +
            "                        '{mimeType}', " +
            "                        '\"test\"'::jsonb " +
            "                    ), " +
            "                    '{extension}', " +
            "                    '\"test\"'::jsonb " +
            "                ), " +
            "                '{displayName}', " +
            "                '\"test\"'::jsonb " +
            "            ) " +
            "        ) " +
            "        FROM jsonb_array_elements(files) AS elem " +
            "    ), " +
            "    '[]'::jsonb " +
            ") " +
            "WHERE id BETWEEN 760 AND 761 " +
            "AND files IS NOT NULL";
