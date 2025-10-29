UPDATE company_doc_version 
SET files = 
    CASE 
        WHEN files IS NOT NULL AND files ? 'id' AND jsonb_typeof(files->'id') = 'string' THEN
            CASE 
                WHEN files IS NOT NULL AND files ? 'md5' AND jsonb_typeof(files->'md5') = 'string' THEN
                    CASE 
                        WHEN files IS NOT NULL AND files ? 'name' AND jsonb_typeof(files->'name') = 'string' THEN
                            jsonb_set(jsonb_set(jsonb_set(files, '{id}', '"123"'), '{md5}', '"456"'), '{name}', '"test"')
                        ELSE
                            jsonb_set(jsonb_set(files, '{id}', '"123"'), '{md5}', '"456"')
                    END
                WHEN files IS NOT NULL AND files ? 'name' AND jsonb_typeof(files->'name') = 'string' THEN
                    jsonb_set(jsonb_set(files, '{id}', '"123"'), '{name}', '"test"')
                ELSE
                    jsonb_set(files, '{id}', '"123"')
            END
        WHEN files IS NOT NULL AND files ? 'md5' AND jsonb_typeof(files->'md5') = 'string' THEN
            CASE 
                WHEN files IS NOT NULL AND files ? 'name' AND jsonb_typeof(files->'name') = 'string' THEN
                    jsonb_set(jsonb_set(files, '{md5}', '"456"'), '{name}', '"test"')
                ELSE
                    jsonb_set(files, '{md5}', '"456"')
            END
        WHEN files IS NOT NULL AND files ? 'name' AND jsonb_typeof(files->'name') = 'string' THEN
            jsonb_set(files, '{name}', '"test"')
        ELSE
            files
    END
WHERE id BETWEEN 701 AND 702;
