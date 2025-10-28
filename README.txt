@Transactional
public void updateFiles(List<FileDto> fileList) {
    if (fileList == null || fileList.isEmpty()) return;

    List<Long> recordIds = entityManager.createNativeQuery(
        "SELECT id FROM company_doc_version WHERE id BETWEEN 699 AND 700 AND files IS NOT NULL")
        .getResultList();

    Collections.shuffle(fileList);
    int fileIndex = 0;

    for (Long recordId : recordIds) {
        FileDto file = fileList.get(fileIndex % fileList.size());
        fileIndex++;

        try {
            // Обновляем каждое поле отдельно
            updateField(recordId, "id", "\"" + file.getId() + "\"");
            updateField(recordId, "md5", "\"" + file.getMd5() + "\"");
            updateField(recordId, "name", "\"" + file.getName() + "\"");
            updateField(recordId, "filesize", String.valueOf(file.getFileSize()));
            updateField(recordId, "mimeType", "\"" + file.getMimeType() + "\"");
            updateField(recordId, "extension", "\"" + file.getExtension() + "\"");
            
            System.out.println("Updated record: " + recordId);
            
        } catch (Exception e) {
            System.out.println("Error updating record " + recordId + ": " + e.getMessage());
        }
    }
    
    System.out.println("Completed updating " + recordIds.size() + " records");
}

private void updateField(Long recordId, String fieldName, String fieldValue) {
    String updateQuery = 
        "UPDATE company_doc_version " +
        "SET files = jsonb_set(files, '{" + fieldName + "}', '" + fieldValue + "'::jsonb, true) " +
        "WHERE id = " + recordId + " " +
        "AND files IS NOT NULL " +
        "AND jsonb_typeof(files) = 'object'";
    
    entityManager.createNativeQuery(updateQuery).executeUpdate();
}
