UPDATE company_doc_version 
SET files = COALESCE(
    (
        SELECT jsonb_agg(
            jsonb_set(
                elem,
                '{md5}',
                '"456"'
            )
        )
        FROM jsonb_array_elements(files) AS elem
    ),
    '[]'::jsonb
)
WHERE id BETWEEN 760 AND 761 
AND files IS NOT NULL;
