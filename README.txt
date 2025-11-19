@Override
    @Transactional
    @CacheEvict(value = {"productSearch", "productByKeyAndDate", "productByKeyAndDateValid", "search", "countByFilter"}, allEntries = true)
    public void create(Product product, LocalDateTime date) {
        if (date == null) {
            date = now();
        }
        Offering offering = offeringService.getOfferingByKeyAndDate(product.getOfferingKey(), date);

        final String productId = UUID.randomUUID().toString();
        product.setId(productId);
        product.setVersion(productMapper.getNextVersion(product.getBusinessKey()));
        product.setValidFrom(product.getValidFrom() == null ? now() : product.getValidFrom());

        List<Product> previousProductsList = productMapper.getProductsByKey(product.getBusinessKey());

        if (!CollectionUtils.isEmpty(previousProductsList)) {
            Optional<Product> maxProduct = previousProductsList
                    .stream()
                    .max(Comparator.comparingInt((Product::getVersion)));
            if (maxProduct.isPresent()) {

                Product previousProduct = maxProduct.get();
                LocalDateTime previousValidFrom = previousProduct.getValidFrom();
                if (Objects.nonNull(previousValidFrom) && !product.getValidFrom().isAfter(previousValidFrom)) {
                    throw new ValidateException(String.format("Previous previousValidFrom [%s] is bigger or equal than validFrom [%s]",
                            previousValidFrom, product.getValidFrom()));
                }

                LocalDateTime previousValidTo = previousProduct.getValidTo();
                if (Objects.nonNull(previousValidTo) && (!product.getValidFrom().isAfter(previousValidTo))) {
                        throw new ValidateException(String.format("Previous validTo [%s] is bigger than validFrom [%s]",
                                previousValidTo, product.getValidFrom()));
                    }


                if (Objects.isNull(previousValidTo)) {
                    productMapper.updateValidToColumn(previousProduct.getBusinessKey(), previousProduct.getVersion(), product.getValidFrom());
                }
            }
        }
        productMapper.create(product);

        // характеристики
        if (Lists.isNullOrEmpty(product.getCharacteristicLinks()))
            product.setCharacteristicLinks(emptyList());

        // матрица ценообразования
        if (product.getPriceMatrix() != null)
            priceMatrixService.create(PRODUCT, productId, product.getPriceMatrix());

        List<String> validateMessages = product.getCharacteristicLinks().stream()
                .map(characteristicLink -> characteristicService.createCharacteristicLink
                        (PRODUCT, product, OFFERING, offering.getId(), characteristicLink))
                .flatMap(Collection::stream)
                .collect(Collectors.toList());

        if (!Lists.isNullOrEmpty(validateMessages))
            throw new ValidateException(validateMessages);
    }
