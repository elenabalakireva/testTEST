UPDATE company_doc_version 
SET files = 
    CASE 
        WHEN files ?| array['id','md5','name'] THEN 
            jsonb_set(
                jsonb_set(
                    jsonb_set(files, '{id}', '"123"', false),
                    '{md5}', '"456"', false
                ),
                '{name}', '"test"', false
            )
        ELSE files
    END
WHERE files IS NOT NULL;
