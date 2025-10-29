UPDATE company_doc_version 
SET files = jsonb_set(files, '{md5}', '"456"')
WHERE files IS NOT NULL 
  AND files ? 'md5' 
  AND jsonb_typeof(files->'md5') = 'string'
  AND id BETWEEN 701 AND 702;
