@Override
@Transactional
@CacheEvict(value = {"productSearch", "productByKeyAndDate", "productByKeyAndDateValid", "search", "countByFilter"}, allEntries = true)
public void create(Product product, LocalDateTime date) {
    prepareProductData(product, date);
    validateAndUpdatePreviousProducts(product);
    createProductInDatabase(product);
    createProductCharacteristics(product);
    validateCharacteristicCreation(product);
}

private void prepareProductData(Product product, LocalDateTime date) {
    if (date == null) {
        date = now();
    }
    
    Offering offering = offeringService.getOfferingByKeyAndDate(product.getOfferingKey(), date);
    final String productId = UUID.randomUUID().toString();
    
    product.setId(productId);
    product.setVersion(productMapper.getNextVersion(product.getBusinessKey()));
    product.setValidFrom(product.getValidFrom() == null ? now() : product.getValidFrom());
}

private void validateAndUpdatePreviousProducts(Product product) {
    List<Product> previousProductsList = productMapper.getProductsByKey(product.getBusinessKey());
    
    if (CollectionUtils.isEmpty(previousProductsList)) {
        return;
    }
    
    Product latestProduct = findLatestProduct(previousProductsList);
    validateProductDates(product, latestProduct);
    updatePreviousProductValidTo(product, latestProduct);
}

private Product findLatestProduct(List<Product> previousProductsList) {
    return previousProductsList
            .stream()
            .max(Comparator.comparingInt(Product::getVersion))
            .orElseThrow(() -> new ValidateException("No previous product found"));
}

private void validateProductDates(Product product, Product previousProduct) {
    LocalDateTime previousValidFrom = previousProduct.getValidFrom();
    if (Objects.nonNull(previousValidFrom) && !product.getValidFrom().isAfter(previousValidFrom)) {
        throw new ValidateException(String.format("Previous validFrom [%s] is bigger or equal than validFrom [%s]",
                previousValidFrom, product.getValidFrom()));
    }

    LocalDateTime previousValidTo = previousProduct.getValidTo();
    if (Objects.nonNull(previousValidTo) && !product.getValidFrom().isAfter(previousValidTo)) {
        throw new ValidateException(String.format("Previous validTo [%s] is bigger than validFrom [%s]",
                previousValidTo, product.getValidFrom()));
    }
}

private void updatePreviousProductValidTo(Product product, Product previousProduct) {
    if (Objects.isNull(previousProduct.getValidTo())) {
        productMapper.updateValidToColumn(previousProduct.getBusinessKey(), 
                                        previousProduct.getVersion(), 
                                        product.getValidFrom());
    }
}

private void createProductInDatabase(Product product) {
    productMapper.create(product);
    
    // матрица ценообразования
    if (product.getPriceMatrix() != null) {
        priceMatrixService.create(PRODUCT, product.getId(), product.getPriceMatrix());
    }
}

private void createProductCharacteristics(Product product) {
    if (Lists.isNullOrEmpty(product.getCharacteristicLinks())) {
        product.setCharacteristicLinks(emptyList());
    }
}

private void validateCharacteristicCreation(Product product) {
    Offering offering = offeringService.getOfferingByKeyAndDate(product.getOfferingKey(), now());
    
    List<String> validateMessages = product.getCharacteristicLinks().stream()
            .map(characteristicLink -> characteristicService.createCharacteristicLink(
                    PRODUCT, product, OFFERING, offering.getId(), characteristicLink))
            .flatMap(Collection::stream)
            .collect(Collectors.toList());

    if (!Lists.isNullOrEmpty(validateMessages)) {
        throw new ValidateException(validateMessages);
    }
}
