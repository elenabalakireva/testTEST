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

    File mainFile = shuffledFiles.get(0);
    String mainFileJson = String.format(
        "{\"id\":\"%s\",\"md5\":\"%s\",\"name\":\"%s\",\"filesize\":%d,\"mimeType\":\"%s\",\"extension\":\"%s\"}",
        mainFile.getId(), mainFile.getMd5(), mainFile.getName(), 
        mainFile.getFileSize(), mainFile.getMimeType(), mainFile.getExtension()
    );

    // Остальные файлы для не-максимальных версий
    List<File> randomFiles = shuffledFiles.subList(1, shuffledFiles.size());
    if (randomFiles.isEmpty()) {
        randomFiles = List.of(mainFile);
    }

    // Подготавливаем случайные файлы как массив JSON
    String randomFilesArray = randomFiles.stream()
        .map(file -> String.format(
            "{\"id\":\"%s\",\"md5\":\"%s\",\"name\":\"%s\",\"filesize\":%d,\"mimeType\":\"%s\",\"extension\":\"%s\"}",
            file.getId(), file.getMd5(), file.getName(), 
            file.getFileSize(), file.getMimeType(), file.getExtension()
        ))
        .collect(Collectors.joining(",", "[", "]"));

    // Запрос для обновления версий с использованием random()
    String updateVersionsQuery = 
        "WITH latest_versions AS (" +
        "    SELECT company_doc_id, MAX(version) as max_version " +
        "    FROM company_doc_version " +
        "    GROUP BY company_doc_id" +
        ") " +
        "UPDATE company_doc_version " +
        "SET files = CASE " +
        "    WHEN (company_doc_id, version) IN (SELECT company_doc_id, max_version FROM latest_versions) " +
        "    THEN CAST(?1 AS jsonb) " +
        "    ELSE (" +
        "        SELECT CAST(files_data AS jsonb) FROM (" +
        "            SELECT files_data, row_number() over () as rn " +
        "            FROM unnest(CAST(?2 AS jsonb[])) AS files_data" +
        "        ) f " +
        "        WHERE rn = 1 + floor(random() * ?3)::int " +
        "        LIMIT 1" +
        "    ) " +
        "END " +
        "WHERE files IS NOT NULL";

    // Обновляем company_doc
    entityManager.createNativeQuery("UPDATE company_doc SET files = CAST(?1 AS jsonb) WHERE files IS NOT NULL")
        .setParameter(1, mainFileJson)
        .executeUpdate();

    // Обновляем company_doc_version
    entityManager.createNativeQuery(updateVersionsQuery)
        .setParameter(1, mainFileJson)
        .setParameter(2, randomFilesArray)
        .setParameter(3, randomFiles.size())
        .executeUpdate();
}
