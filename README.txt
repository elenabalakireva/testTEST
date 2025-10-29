UPDATE company_doc_version 
SET files = COALESCE(
    (
        SELECT jsonb_agg(
            jsonb_set(
                jsonb_set(
                    CASE 
                        WHEN elem ? 'id' THEN jsonb_set(elem, '{id}', '"123"')
                        ELSE elem
                    END,
                    '{md5}',
                    CASE WHEN elem ? 'md5' THEN '"456"' ELSE elem->'md5' END
                ),
                '{name}',
                CASE WHEN elem ? 'name' THEN '"test"' ELSE elem->'name' END
            )
        )
        FROM jsonb_array_elements(files) AS elem
    ),
    '[]'::jsonb
)
WHERE id BETWEEN 701 AND 702 
  AND files IS NOT NULL;
