UPDATE company_doc 
SET files = (
    SELECT cdv.files 
    FROM company_doc_version cdv 
    WHERE cdv.company_doc_id = company_doc.id 
    AND cdv.version = (
        SELECT MAX(version) 
        FROM company_doc_version 
        WHERE company_doc_id = company_doc.id
    )
)
WHERE company_doc.id IN (
    SELECT DISTINCT company_doc_id 
    FROM company_doc_version 
    WHERE id BETWEEN 760 AND 761
);
