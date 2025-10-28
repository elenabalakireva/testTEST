@Transactional
public void updateFiles(List<FileDto> fileList) {
    if (fileList == null || fileList.isEmpty()) return;

    // Пробуем жестко закодированное значение с ПРАВИЛЬНЫМИ кавычками
    String updateQuery = 
        "UPDATE company_doc_version " +
        "SET files = jsonb_set(files, '{id}', '\"test-id-123\"'::jsonb) " +  // ← ОДИНАРНЫЕ кавычки снаружи
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
