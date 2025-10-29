UPDATE company_doc_version 
SET files = (
    SELECT jsonb_agg(
        CASE 
            WHEN elem ? 'md5' THEN jsonb_set(elem, '{md5}', '"456"')
            ELSE elem
        END
    )
    FROM jsonb_array_elements(files) AS elem
)
WHERE id BETWEEN 701 AND 702 
  AND files IS NOT NULL 
  AND jsonb_typeof(files) = 'array';
