    public List<CharacteristicLink> getCharacteristicLinksByOfferingIds(EntityTypeEnum entityType, List<String> entityIds) {
        log.debug("getCharacteristicLinksByOfferingIds: entityType = {}, entityIds = {}", entityType, entityIds);

        List<CharacteristicLink> characteristicLinks;

        if (CollectionUtils.isEmpty(entityIds)) {
            return new ArrayList<>();
        }

        if (entityType == EntityTypeEnum.OFFERING) {
            log.info("entityIds = {}", entityIds);
            characteristicLinks = characteristicMapper.getOfferingCharacteristicLinks(entityIds);
        } else {
            log.info("entityType = {}, entityIds = {}", entityType, entityIds);
            characteristicLinks = characteristicMapper.getCharacteristicLinks(entityType, entityIds);
        }
        log.debug("characteristicLinks = {}", characteristicLinks);

        loadCharacteristicValues(characteristicLinks);
        loadCharacteristicValueConditions(characteristicLinks);

        log.debug("characteristicLinks with values = {}", characteristicLinks);

        return characteristicLinks;
    }

    private void loadCharacteristicValues(List<CharacteristicLink> characteristicLinks) {
        List<String> characteristicLinkIds = characteristicLinks.stream()
                .map(CharacteristicLink::getId)
                .collect(Collectors.toList());

        if (isNotEmpty(characteristicLinkIds)) {
            List<Value> values = characteristicMapper.getCharacteristicValuesByIds(characteristicLinkIds);
            for (Value value : values) {
                for (CharacteristicLink characteristicLink : characteristicLinks) {
                    if (characteristicLink.getId().equals(value.getCharId())) {
                        if (characteristicLink.getValues() == null) {
                            characteristicLink.setValues(new ArrayList<>());
                        }
                        characteristicLink.getValues().add(value);
                    }
                }
            }
        }
    }

    private void loadCharacteristicValueConditions(List<CharacteristicLink> characteristicLinks) {
        List<String> valuesIds = characteristicLinks.stream()
                .flatMap(c -> c.getValues().stream())
                .map(CommonDto::getId)
                .collect(Collectors.toList());

        if (CollectionUtils.isEmpty(valuesIds)) {
            return;
        }
        log.debug("valuesIds = {}", characteristicLinks);

        List<ValueCondition> results = characteristicMapper.getCharacteristicValueConditions(valuesIds);
        for (ValueCondition result : results) {
            if (result == null) {
                continue;
            }
            for (CharacteristicLink characteristicLink : characteristicLinks) {
                if (characteristicLink.getValues() == null) {
                    continue;
                }
                setConditions(characteristicLink, result);
            }
        }
    }

    private void setConditions(CharacteristicLink characteristicLink, ValueCondition result) {
        for (Value value : characteristicLink.getValues()) {
            if (value.getId().equals(result.getValueId())) {
                if (value.getConditions() == null) {
                    value.setConditions(new ArrayList<>());
                }
                value.getConditions().add(result);
            }
        }
    }
