SELECT *
FROM company_doc_version
WHERE (company_doc_id, version) IN (
    SELECT company_doc_id, MAX(version)
    FROM company_doc_version
    WHERE company_doc_id BETWEEN 481 AND 482
    GROUP BY company_doc_id
);
