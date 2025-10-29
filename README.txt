UPDATE company_doc_version 
SET files = COALESCE(
    (
        SELECT jsonb_agg(
            jsonb_set(
                jsonb_set(
                    jsonb_set(
                        jsonb_set(
                            jsonb_set(
                                jsonb_set(
                                    elem,
                                    '{id}',
                                    '"123"'
                                ),
                                '{md5}',
                                '"456"'
                            ),
                            '{name}',
                            '"test"'
                        ),
                        '{filesize}',
                        '1'
                    ),
                    '{mimeType}',
                    '"test"'
                ),
                '{extension}',
                '"test"'
            )
        )
        FROM jsonb_array_elements(files) AS elem
    ),
    '[]'::jsonb
)
WHERE id BETWEEN 701 AND 702 
  AND files IS NOT NULL;
