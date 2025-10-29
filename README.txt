@Repository
public class CompanyDocRepository {

    @Autowired
    private JdbcTemplate jdbcTemplate;

    @Transactional
    public void updateFiles(FileDto file) {
        String sql = "UPDATE company_doc_version " +
            "SET files = COALESCE( " +
            "    ( " +
            "        SELECT jsonb_agg( " +
            "            jsonb_set( " +
            "                jsonb_set( " +
            "                    jsonb_set( " +
            "                        jsonb_set( " +
            "                            jsonb_set( " +
            "                                jsonb_set( " +
            "                                    jsonb_set( " +
            "                                        elem, " +
            "                                        '{id}', " +
            "                                        ? " +
            "                                    ), " +
            "                                    '{md5}', " +
            "                                    ? " +
            "                                ), " +
            "                                '{name}', " +
            "                                ? " +
            "                            ), " +
            "                            '{filesize}', " +
            "                            ? " +
            "                        ), " +
            "                        '{mimeType}', " +
            "                        ? " +
            "                    ), " +
            "                    '{extension}', " +
            "                    ? " +
            "                ), " +
            "                '{displayName}', " +
            "                ? " +
            "            ) " +
            "        ) " +
            "        FROM jsonb_array_elements(files) AS elem " +
            "    ), " +
            "    '[]'::jsonb " +
            ") " +
            "WHERE id BETWEEN 760 AND 761 " +
            "AND files IS NOT NULL";

        int updatedRows = jdbcTemplate.update(sql,
            "\"" + file.getId() + "\"",
            "\"" + file.getMd5() + "\"",
            "\"" + file.getName() + "\"",
            file.getFileSize(),
            "\"" + file.getMimeType() + "\"",
            "\"" + file.getExtension() + "\"",
            "\"" + file.getDisplayName() + "\""
        );
        
        System.out.println("Updated rows: " + updatedRows);
    }
}
