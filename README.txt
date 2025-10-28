@Transactional
public void updateFiles(List<FileDto> fileList) {
    if (fileList == null || fileList.isEmpty()) return;

    FileDto file = fileList.get(0);
    
    // Обновляем только файловые поля, сохраняя остальные
    String updateQuery = 
        "UPDATE company_doc_version " +
        "SET files = (" +
        "    SELECT jsonb_build_object(" +
        "        'id', '\"" + file.getId() + "\"'," +
        "        'md5', '\"" + file.getMd5() + "\"'," +
        "        'name', '\"" + file.getName() + "\"'," +
        "        'filesize', " + file.getFileSize() + "," +
        "        'mimeType', '\"" + file.getMimeType() + "\"'," +
        "        'extension', '\"" + file.getExtension() + "\"'," +
        "        'user', files->'user'," +
        "        'certInfo', files->'certInfo'," +
        "        'versions', files->'versions'," +
        "        'coldStore', files->'coldStore'," +
        "        'objectName', files->'objectName'," +
        "        'objectPath', files->'objectPath'," +
        "        'displayName', files->'displayName'," +
        "        'objectStatus', files->'objectStatus'," +
        "        'baseVersionId', files->'baseVersionId'," +
        "        'changedAuthor', files->'changedAuthor'," +
        "        'createdAuthor', files->'createdAuthor'," +
        "        'signedFileName', files->'signedFileName'" +
        "    ) FROM company_doc_version WHERE id = 699" +
        ") " +
        "WHERE id = 699";

    System.out.println("SQL: " + updateQuery);
    
    try {
        int updated = entityManager.createNativeQuery(updateQuery).executeUpdate();
        System.out.println("Updated rows: " + updated);
        
    } catch (Exception e) {
        System.out.println("Error: " + e.getMessage());
        e.printStackTrace();
    }
}
