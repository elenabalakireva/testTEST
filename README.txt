UPDATE company_doc_version 
SET files = jsonb_set(
    files,
    '{persons}',
    COALESCE(
        (
            SELECT jsonb_agg(
                CASE 
                    WHEN person->'id' IS NOT NULL THEN
                        person || jsonb_build_object('id', id::text)
                    ELSE
                        person
                END
            )
            FROM jsonb_array_elements(
                CASE 
                    WHEN jsonb_typeof(files->'persons') = 'array'
                    THEN files->'persons'
                    ELSE '[]'::jsonb
                END
            ) AS person
        ),
        '[]'::jsonb
    )
)
WHERE files IS NOT NULL 
AND jsonb_typeof(files->'persons') = 'array' 
AND id BETWEEN 40 AND 45;
