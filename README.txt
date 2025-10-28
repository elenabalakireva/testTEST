String updateQuery = 
        "UPDATE company_doc_version " +
        "SET files = jsonb_set(files, '{id}', '\"" + file.getId() + "\"'::jsonb) " +
        "WHERE id = 699";
