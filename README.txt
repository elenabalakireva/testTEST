@Transactional
public void updateFiles(List<FileDto> fileList) {
    if (fileList == null || fileList.isEmpty()) return;

    FileDto file = fileList.get(0);
    
    String updateQuery = 
        "UPDATE company_doc_version " +
        "SET files = jsonb_set(" +
        "    jsonb_set(" +
        "        jsonb_set(" +
        "            jsonb_set(" +
        "                jsonb_set(" +
        "                    jsonb_set(" +
        "                        files, " +
        "                        '{id}', " +
        "                        '\"" + file.getId() + "\"'::jsonb" +
        "                    ), " +
        "                    '{md5}', " +
        "                    '\"" + file.getMd5() + "\"'::jsonb" +
        "                ), " +
        "                '{name}', " +
        "                '\"" + file.getName() + "\"'::jsonb" +
        "            ), " +
        "            '{filesize}', " +
        "            to_jsonb(" + file.getFileSize() + ")" +
        "        ), " +
        "        '{mimeType}', " +
        "        '\"" + file.getMimeType() + "\"'::jsonb" +
        "    ), " +
        "    '{extension}', " +
        "    '\"" + file.getExtension() + "\"'::jsonb" +
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
