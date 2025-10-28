
@Repository
public class CompanyDocRepository {

    @PersistenceContext
    private EntityManager entityManager;

    public void updateFilesRandomlyV2(List<File> fileList) {
        if (fileList == null || fileList.isEmpty()) {
            return;
        }

        // Перемешиваем все файлы
        List<File> shuffledFiles = new ArrayList<>(fileList);
        Collections.shuffle(shuffledFiles);

        // Создаем массив параметров для случайного выбора
        String filesArray = "{" + shuffledFiles.stream()
            .map(file -> String.format(
                "\"{\\\"id\\\":\\\"%s\\\",\\\"md5\\\":\\\"%s\\\",\\\"name\\\":\\\"%s\\\",\\\"filesize\\\":%d,\\\"mimeType\\\":\\\"%s\\\",\\\"extension\\\":\\\"%s\\\"}\"",
                file.getId(), file.getMd5(), file.getName(), 
                file.getFileSize(), file.getMimeType(), file.getExtension()
            ))
            .collect(Collectors.joining(",")) + "}";

        // Для каждой записи выбираем случайный файл из массива и строим JSON
        String updateVersionsQuery = 
            "UPDATE company_doc_version " +
            "SET files = (" +
            "    SELECT jsonb_build_object(" +
            "        'id', (elem::jsonb)->>'id', " +
            "        'md5', (elem::jsonb)->>'md5', " +
            "        'name', (elem::jsonb)->>'name', " +
            "        'filesize', ((elem::jsonb)->>'filesize')::bigint, " +
            "        'mimeType', (elem::jsonb)->>'mimeType', " +
            "        'extension', (elem::jsonb)->>'extension'" +
            "    ) " +
            "    FROM unnest(CAST(?1 AS text[])) AS elem " +
            "    ORDER BY random() " +
            "    LIMIT 1" +
            ") " +
            "WHERE files IS NOT NULL";

        entityManager.createNativeQuery(updateVersionsQuery)
            .setParameter(1, filesArray)
            .executeUpdate();
    }
}
