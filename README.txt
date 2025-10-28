@Transactional
public void updateFiles(List<FileDto> fileList) {
    if (fileList == null || fileList.isEmpty()) return;

    FileDto file = fileList.get(0);
    
    // Простой JSON объект
    String updateQuery = 
        "UPDATE company_doc_version " +
        "SET files = '{\"id\":\"" + file.getId() + "\"}' " +
        "WHERE id = 699";

    System.out.println("SQL: " + updateQuery);
    
    try {
        int updated = entityManager.createNativeQuery(updateQuery).executeUpdate();
        System.out.println("Updated rows: " + updated);
        
        // Проверим результат
        Object result = entityManager.createNativeQuery(
            "SELECT files->>'id' FROM company_doc_version WHERE id = 699")
            .getSingleResult();
        System.out.println("New ID in DB: " + result);
        
    } catch (Exception e) {
        System.out.println("Error: " + e.getMessage());
        e.printStackTrace();
    }
}
