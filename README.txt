String updateQuery = 
        "UPDATE company_doc_version " +
        "SET files = jsonb_set(" +
        "    jsonb_set(" +
        "        jsonb_set(" +
        "            jsonb_set(" +
        "                jsonb_set(" +
        "                    jsonb_set(" +
        "                        files, " +
        "                        '{id}', " +
        "                        '\"" + file.getId() + "\"'::jsonb" +
        "                    ), " +
        "                    '{md5}', " +
        "                    '\"" + file.getMd5() + "\"'::jsonb" +
        "                ), " +
        "                '{name}', " +
        "                '\"" + file.getName() + "\"'::jsonb" +
        "            ), " +
        "            '{filesize}', " +
        "            '" + file.getFileSize() + "'::jsonb" +
        "        ), " +
        "        '{mimeType}', " +
        "        '\"" + file.getMimeType() + "\"'::jsonb" +
        "    ), " +
        "    '{extension}', " +
        "    '\"" + file.getExtension() + "\"'::jsonb" +
        ") " +
        "WHERE files IS NOT NULL " +
        "AND jsonb_typeof(files) = 'object' " +  // проверяем что это JSON объект
        "AND id BETWEEN 699 AND 700";
