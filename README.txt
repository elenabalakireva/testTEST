@Transactional
public void updateFiles(List<FileDto> fileList) {
    if (fileList == null || fileList.isEmpty()) return;

    // Сначала проверим существует ли запись
    try {
        Long count = (Long) entityManager.createNativeQuery(
            "SELECT COUNT(*) FROM company_doc_version WHERE id = 699")
            .getSingleResult();
        System.out.println("Records with id=699: " + count);
        
        if (count == 0) {
            System.out.println("No record with id=699 found!");
            return;
        }
        
        // Простой update
        String updateQuery = "UPDATE company_doc_version SET files = '{\"test\":\"value\"}' WHERE id = 699";
        System.out.println("SQL: " + updateQuery);
        
        int updated = entityManager.createNativeQuery(updateQuery).executeUpdate();
        System.out.println("Updated rows: " + updated);
        
    } catch (Exception e) {
        System.out.println("Error: " + e.getMessage());
        e.printStackTrace();
    }
}
