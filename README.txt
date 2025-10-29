@Repository
public class CompanyDocRepository {

    @Autowired
    private JdbcTemplate jdbcTemplate;

    @Transactional
    public void updateFiles(List<FileDto> files) {
        if (files == null || files.isEmpty()) {
            return;
        }

        int maxArrayLength = getMaxArrayLength();
        for (int i = 0; i < maxArrayLength; i++) {
            FileDto randomFile = files.get(new Random().nextInt(files.size()));
            updateFileFieldsAtIndex(i, randomFile);
        }
    }

    private void updateFileFieldsAtIndex(int index, FileDto file) {
        String sql = "UPDATE company_doc_version " +
            "SET files = jsonb_set( " +
            "    files, " +
            "    '{${index}}', " +
            "    jsonb_set( " +
            "        jsonb_set( " +
            "            jsonb_set( " +
            "                jsonb_set( " +
            "                    jsonb_set( " +
            "                        jsonb_set( " +
            "                            jsonb_set( " +
            "                                files->${index}, " +
            "                                '{id}', " +
            "                                to_jsonb(?) " +
            "                            ), " +
            "                            '{md5}', " +
            "                            to_jsonb(?) " +
            "                        ), " +
            "                        '{name}', " +
            "                        to_jsonb(?) " +
            "                    ), " +
            "                    '{filesize}', " +
            "                    to_jsonb(?) " +
            "                ), " +
            "                '{mimeType}', " +
            "                to_jsonb(?) " +
            "            ), " +
            "            '{extension}', " +
            "            to_jsonb(?) " +
            "        ), " +
            "        '{displayName}', " +
            "        to_jsonb(?) " +
            "    ) " +
            ") " +
            "WHERE id BETWEEN 760 AND 761 " +
            "AND files IS NOT NULL " +
            "AND jsonb_array_length(files) > ?";

        sql = sql.replace("${index}", String.valueOf(index));

        jdbcTemplate.update(sql,
            file.getId(), 
            file.getMd5(), 
            file.getName(),
            file.getFileSize(), 
            file.getMimeType(), 
            file.getExtension(), 
            file.getDisplayName(), 
            index
        );
    }

    private int getMaxArrayLength() {
        String sql = "SELECT MAX(jsonb_array_length(files)) FROM company_doc_version WHERE id BETWEEN 760 AND 761";
        Integer maxLength = jdbcTemplate.queryForObject(sql, Integer.class);
        return maxLength != null ? maxLength : 0;
    }
}
