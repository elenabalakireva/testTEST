@Transactional
public void updateFiles(List<FileDto> fileList) {
    if (fileList == null || fileList.isEmpty()) return;

    // Берем только первый файл для теста
    FileDto file = fileList.get(0);
    
    // САМЫЙ ПРОСТОЙ запрос без массивов
    String updateQuery = 
        "UPDATE company_doc_version " +
        "SET files = jsonb_build_object(" +
        "    'id', '" + file.getId() + "', " +
        "    'md5', '" + file.getMd5() + "', " +
        "    'name', '" + file.getName() + "', " +
        "    'filesize', " + file.getFileSize() + ", " +
        "    'mimeType', '" + file.getMimeType() + "', " +
        "    'extension', '" + file.getExtension() + "'" +
        ") " +
        "WHERE files IS NOT NULL AND id BETWEEN 699 AND 700";

    System.out.println("SQL: " + updateQuery); // ← ВЫВЕДЕМ ЗАПРОС
    
    try {
        int updated = entityManager.createNativeQuery(updateQuery).executeUpdate();
        System.out.println("Updated rows: " + updated);
    } catch (Exception e) {
        System.out.println("Error: " + e.getMessage());
        throw new UpdateDataBaseException("SQL Error", e);
    }
}
