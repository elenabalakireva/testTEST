UPDATE company_doc_version 
SET files = 
    CASE 
        WHEN jsonb_typeof(files->'id') = 'string' THEN 
            jsonb_set(files, '{id}', '"123"')
        ELSE files
    END,
files = 
    CASE 
        WHEN jsonb_typeof(files->'md5') = 'string' THEN 
            jsonb_set(files, '{md5}', '"456"')
        ELSE files
    END,
files = 
    CASE 
        WHEN jsonb_typeof(files->'name') = 'string' THEN 
            jsonb_set(files, '{name}', '"test"')
        ELSE files
    END
WHERE files IS NOT NULL;
