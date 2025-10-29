SELECT id, files->'md5' as current_md5, jsonb_typeof(files->'md5') as md5_type
FROM company_doc_version 
WHERE id BETWEEN 701 AND 702;
