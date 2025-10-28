// Создаем SQL массив
    String sqlArray = fileList.stream()
        .map(file -> String.format(
            "'{\"id\":\"%s\",\"md5\":\"%s\",\"name\":\"%s\",\"filesize\":%d,\"mimeType\":\"%s\",\"extension\":\"%s\"}'",
            file.getId(), file.getMd5(), file.getName(),
            file.getFileSize(), file.getMimeType(), file.getExtension()
        ))
        .collect(Collectors.joining(", "));

    String updateQuery = 
        "UPDATE company_doc_version " +
        "SET files = (" +
        "    SELECT jsonb_build_object(" +
        "        'id', (elem::jsonb)->>'id', " +  // ->> для текста
        "        'md5', (elem::jsonb)->>'md5', " +
        "        'name', (elem::jsonb)->>'name', " +
        "        'filesize', ((elem::jsonb)->>'filesize')::bigint, " +  // :: вместо ->
        "        'mimeType', (elem::jsonb)->>'mimeType', " +
        "        'extension', (elem::jsonb)->>'extension'" +
        "    ) " +
        "    FROM unnest(ARRAY[" + sqlArray + "]) AS elem " +
        "    ORDER BY random() " +
        "    LIMIT 1" +
        ") " +
        "WHERE files IS NOT NULL AND id BETWEEN 699 AND 700";

    try {
        entityManager.createNativeQuery(updateQuery)
            .executeUpdate();  // БЕЗ setParameter!
        System.out.println("@@@");
    } catch (Exception e) {
        throw new UpdateDataBaseException("!!!!!" + e, e);
    }
