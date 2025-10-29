SELECT cdv.* 
FROM company_doc_version cdv
WHERE cdv.company_doc_id BETWEEN 481 AND 482
AND cdv.version = (
    SELECT MAX(version) 
    FROM company_doc_version 
    WHERE company_doc_id = cdv.company_doc_id
);
