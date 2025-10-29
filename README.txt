UPDATE company_doc_version 
SET files = jsonb_set(files, '{md5}', '"456"')
WHERE id BETWEEN 701 AND 702 
  AND files IS NOT NULL 
  AND files ? 'md5';
