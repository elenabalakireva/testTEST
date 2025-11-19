public List<CharacteristicLink> getCharacteristicLinksByOfferingIds(EntityTypeEnum entityType, List<String> entityIds) {
    log.debug("getCharacteristicLinksByOfferingIds: entityType = {}, entityIds = {}", entityType, entityIds);

    if (CollectionUtils.isEmpty(entityIds)) {
        return new ArrayList<>();
    }

    List<CharacteristicLink> characteristicLinks = fetchCharacteristicLinks(entityType, entityIds);
    log.debug("characteristicLinks = {}", characteristicLinks);

    enrichCharacteristicLinksWithValues(characteristicLinks);
    log.debug("characteristicLinks with values = {}", characteristicLinks);

    enrichValuesWithConditions(characteristicLinks);
    log.debug("characteristicLinks with conditions = {}", characteristicLinks);

    return characteristicLinks;
}

private List<CharacteristicLink> fetchCharacteristicLinks(EntityTypeEnum entityType, List<String> entityIds) {
    if (entityType == EntityTypeEnum.OFFERING) {
        log.info("entityIds = {}", entityIds);
        return characteristicMapper.getOfferingCharacteristicLinks(entityIds);
    } else {
        log.info("entityType = {}, entityIds = {}", entityType, entityIds);
        return characteristicMapper.getCharacteristicLinks(entityType, entityIds);
    }
}

private void enrichCharacteristicLinksWithValues(List<CharacteristicLink> characteristicLinks) {
    List<String> characteristicLinkIds = characteristicLinks.stream()
            .map(CharacteristicLink::getId)
            .collect(Collectors.toList());

    if (CollectionUtils.isEmpty(characteristicLinkIds)) {
        return;
    }

    List<Value> values = characteristicMapper.getCharacteristicValuesByIds(characteristicLinkIds);
    Map<String, List<Value>> valuesByCharId = values.stream()
            .collect(Collectors.groupingBy(Value::getCharId));

    for (CharacteristicLink characteristicLink : characteristicLinks) {
        List<Value> linkValues = valuesByCharId.get(characteristicLink.getId());
        if (linkValues != null) {
            characteristicLink.setValues(new ArrayList<>(linkValues));
        }
    }
}

private void enrichValuesWithConditions(List<CharacteristicLink> characteristicLinks) {
    List<String> valuesIds = characteristicLinks.stream()
            .filter(Objects::nonNull)
            .map(CharacteristicLink::getValues)
            .filter(Objects::nonNull)
            .flatMap(List::stream)
            .map(CommonDto::getId)
            .collect(Collectors.toList());

    if (CollectionUtils.isEmpty(valuesIds)) {
        return;
    }

    List<ValueCondition> results = characteristicMapper.getCharacteristicValueConditions(valuesIds);
    Map<String, List<ValueCondition>> conditionsByValueId = results.stream()
            .filter(Objects::nonNull)
            .collect(Collectors.groupingBy(ValueCondition::getValueId));

    for (CharacteristicLink characteristicLink : characteristicLinks) {
        if (characteristicLink.getValues() == null) {
            continue;
        }
        for (Value value : characteristicLink.getValues()) {
            List<ValueCondition> valueConditions = conditionsByValueId.get(value.getId());
            if (valueConditions != null) {
                value.setConditions(new ArrayList<>(valueConditions));
            }
        }
    }
}
