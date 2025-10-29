@Autowired
    private JdbcTemplate jdbcTemplate;

    @Transactional
    public void updateFiles(List<FileDto> files) {
        if (files == null || files.isEmpty()) {
            return;
        }

        // Для каждого индекса в массиве files выбираем случайный FileDto
        for (int i = 0; i < getMaxArrayLength(); i++) {
            FileDto randomFile = files.get(new Random().nextInt(files.size()));
            updateFileAtIndex(i, randomFile);
        }
    }

    private void updateFileAtIndex(int index, FileDto file) {
        String sql = "UPDATE company_doc_version " +
            "SET files = jsonb_set( " +
            "    files, " +
            "    '{${index}}', " +
            "    jsonb_build_object( " +
            "        'id', ?, " +
            "        'md5', ?, " +
            "        'name', ?, " +
            "        'filesize', ?, " +
            "        'mimeType', ?, " +
            "        'extension', ?, " +
            "        'displayName', ? " +
            "    ) " +
            ") " +
            "WHERE id BETWEEN 760 AND 761 " +
            "AND files IS NOT NULL " +
            "AND jsonb_array_length(files) > ?";

        sql = sql.replace("${index}", String.valueOf(index));

        jdbcTemplate.update(sql,
            file.getId(), file.getMd5(), file.getName(),
            file.getFileSize(), file.getMimeType(), 
            file.getExtension(), file.getDisplayName(), index
        );
    }

    private int getMaxArrayLength() {
        String sql = "SELECT MAX(jsonb_array_length(files)) FROM company_doc_version WHERE id BETWEEN 760 AND 761";
        Integer maxLength = jdbcTemplate.queryForObject(sql, Integer.class);
        return maxLength != null ? maxLength : 0;
    }
