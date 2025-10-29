UPDATE company_doc_version 
SET files = jsonb_set(
    jsonb_set(
        jsonb_set(
            files,
            '{id}',
            CASE WHEN jsonb_typeof(files->'id') = 'string' THEN '"123"' ELSE files->'id' END
        ),
        '{md5}',
        CASE WHEN jsonb_typeof(files->'md5') = 'string' THEN '"456"' ELSE files->'md5' END
    ),
    '{name}',
    CASE WHEN jsonb_typeof(files->'name') = 'string' THEN '"test"' ELSE files->'name' END
)
WHERE files IS NOT NULL AND id BETWEEN 701 AND 702;
