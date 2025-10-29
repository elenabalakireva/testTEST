UPDATE company_doc_version 
SET files = 
    CASE 
        WHEN files ? 'id' THEN jsonb_set(files, '{id}', '"123"', false)
        ELSE files
    END
WHERE files IS NOT NULL;

UPDATE company_doc_version 
SET files = 
    CASE 
        WHEN files ? 'md5' THEN jsonb_set(files, '{md5}', '"456"', false)
        ELSE files
    END
WHERE files IS NOT NULL;

UPDATE company_doc_version 
SET files = 
    CASE 
        WHEN files ? 'name' THEN jsonb_set(files, '{name}', '"test"', false)
        ELSE files
    END
WHERE files IS NOT NULL;
