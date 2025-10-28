@Transactional
public void updateFiles(List<FileDto> fileList) {
    if (fileList == null || fileList.isEmpty()) return;

    // Сначала проверим что вообще есть в базе
    try {
        Object currentData = entityManager.createNativeQuery(
            "SELECT files FROM company_doc_version WHERE id = 699")
            .getSingleResult();
        System.out.println("Current files in DB: " + currentData);
        
        Object currentId = entityManager.createNativeQuery(
            "SELECT files->>'id' FROM company_doc_version WHERE id = 699")
            .getSingleResult();
        System.out.println("Current ID in DB: " + currentId);
        
    } catch (Exception e) {
        System.out.println("Error reading: " + e.getMessage());
    }
    
    // Пробуем жестко закодированное значение
    String updateQuery = 
        "UPDATE company_doc_version " +
        "SET files = jsonb_set(files, '{id}', '\"test-id-123\"'::jsonb) " +
        "WHERE id = 699";

    System.out.println("SQL: " + updateQuery);
    
    try {
        int updated = entityManager.createNativeQuery(updateQuery).executeUpdate();
        System.out.println("Updated rows: " + updated);
        
        // Проверим результат
        Object newId = entityManager.createNativeQuery(
            "SELECT files->>'id' FROM company_doc_version WHERE id = 699")
            .getSingleResult();
        System.out.println("New ID in DB: " + newId);
        
    } catch (Exception e) {
        System.out.println("Error updating: " + e.getMessage());
        e.printStackTrace();
    }
}
