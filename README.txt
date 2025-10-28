@Transactional
public void updateFiles(List<FileDto> fileList) {
    if (fileList == null || fileList.isEmpty()) return;

    FileDto file = fileList.get(0);
    
    // JSON с несколькими полями
    String updateQuery = 
        "UPDATE company_doc_version " +
        "SET files = '{" +
        "\"id\":\"" + file.getId() + "\"," +
        "\"md5\":\"" + file.getMd5() + "\"," +
        "\"name\":\"" + file.getName() + "\"," +
        "\"filesize\":" + file.getFileSize() + "," +
        "\"mimeType\":\"" + file.getMimeType() + "\"," +
        "\"extension\":\"" + file.getExtension() + "\"" +
        "}' " +
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
