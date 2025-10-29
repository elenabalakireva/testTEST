public void deleteFromS3(List<FileDto> files) {
    ObjectListing objectListing = s3.listObjects(config.getS3Bucket());
    
    log.info("Total objects in S3: " + objectListing.getObjectSummaries().size());
    
    // Собираем ключи, которые НЕ нужно удалять
    Set<String> keysToKeep = files.stream()
        .map(FileDto::getKey)
        .filter(Objects::nonNull)
        .collect(Collectors.toSet());
    
    // Удаляем все файлы, кроме тех, что в keysToKeep
    for (S3ObjectSummary s3ObjectSummary : objectListing.getObjectSummaries()) {
        String objectKey = s3ObjectSummary.getKey();
        if (!keysToKeep.contains(objectKey)) {
            s3.deleteObject(config.getS3Bucket(), objectKey);
            log.info("Deleted object: " + objectKey);
        }
    }
    
    ObjectListing objectListingAfter = s3.listObjects(config.getS3Bucket());
    log.info("Remaining objects in S3: " + objectListingAfter.getObjectSummaries().size());
}
