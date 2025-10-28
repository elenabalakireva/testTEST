
// Создаем массив JSON строк для PostgreSQL
        String filesArray = "ARRAY[" + shuffleFiles.stream()
            .map(file -> String.format(
                "'{\"id\":\"%s\",\"md5\":\"%s\",\"name\":\"%s\",\"filesize\":%d,\"mimeType\":\"%s\",\"extension\":\"%s\"}'",
                file.getId(), 
                file.getMd5(), 
                file.getName(),
                file.getFileSize(), 
                file.getMimeType(), 
                file.getExtension()
            ))
            .collect(Collectors.joining(",")) + "]";
