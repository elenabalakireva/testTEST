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
            "                                        to_jsonb(?::text) " +
            "                                    ), " +
            "                                    '{md5}', " +
            "                                    to_jsonb(?::text) " +
            "                                ), " +
            "                                '{name}', " +
            "                                to_jsonb(?::text) " +
            "                            ), " +
            "                            '{filesize}', " +
            "                            to_jsonb(?::int) " +
            "                        ), " +
            "                        '{mimeType}', " +
            "                        to_jsonb(?::text) " +
            "                    ), " +
            "                    '{extension}', " +
            "                    to_jsonb(?::text) " +
            "                ), " +
            "                '{displayName}', " +
            "                to_jsonb(?::text) " +
            "            ) " +
            "        ) " +
            "        FROM jsonb_array_elements(files) AS elem " +
            "    ), " +
            "    '[]'::jsonb " +
            ") " +
            "WHERE id BETWEEN 760 AND 761 " +
            "AND files IS NOT NULL";

        int updatedRows = jdbcTemplate.update(sql,
            file.getId(),
            file.getMd5(),
            file.getName(),
            file.getFileSize(),
            file.getMimeType(),
            file.getExtension(),
            file.getDisplayName()
        );
        
        System.out.println("Updated rows: " + updatedRows);
    }
}
