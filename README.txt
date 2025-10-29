SELECT cdv.* 
FROM company_doc_version cdv
INNER JOIN (
    SELECT 
        company_doc_id,
        MAX(version) as max_version
    FROM company_doc_version
    WHERE company_doc_id BETWEEN 481 AND 482
    GROUP BY company_doc_id
) latest ON cdv.company_doc_id = latest.company_doc_id 
         AND cdv.version = latest.max_version
WHERE cdv.company_doc_id BETWEEN 481 AND 482;
