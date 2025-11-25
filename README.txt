private static String compareNumberValueAndMaxValue = "number value [%s] is greater than parent characteristic max value [%s]";
    private static String compareNumberValueAndMinValue = "number value [%s] is lesser than parent characteristic min value [%s]";
    private static String compareDecimalValueAndMinValue = "decimal value [%s] is lesser than parent characteristic min value [%s]";
    private static String compareDecimalValueAndMaxValue = "decimal value [%s] is greater than parent characteristic max value [%s]";
    private static String compareDateValueAndMinValue = "date value [%s] is lesser than parent characteristic min value [%s]";

public List<String> validateProduct(CommonCharacteristic upperCharacteristic, Value value) {
        return upperCharacteristic.getValues().stream()
                .map(upperCharacteristicValue -> {
                    var minValue = upperCharacteristicValue.getDecimalMinValue();
                    var currentValue = value.getDecimalValue();
                    var currentDateValue = value.getDateValue();
                    var currentNumberValue = value.getNumberValue();
                    var maxValue = upperCharacteristicValue.getDecimalMaxValue();
                    var upperValue = upperCharacteristicValue.getDecimalValue();
                    var upperNumberMinValue = upperCharacteristicValue.getNumberMinValue();
                    var upperNumberMaxValue = upperCharacteristicValue.getNumberMaxValue();
                    var upperNumberValue = upperCharacteristicValue.getNumberValue();
                    var upperDateMinValue = upperCharacteristicValue.getDateMinValue();
                    var upperDateMaxValue = upperCharacteristicValue.getDateMaxValue();
                    var upperDateValue = upperCharacteristicValue.getDateValue();

                    // *** Попадание точных значений внутрь диапазонов
                    // decimal

                    String rangeDecimalString = rangeDecimal(minValue, maxValue, currentValue, upperValue);
                    if (rangeDecimalString != null) {
                        return rangeDecimalString;
                    }

                    // number

                    String rangeNumberString = rangeNumber(upperNumberMinValue, upperNumberMaxValue, currentNumberValue, upperNumberValue);
                    if (rangeNumberString != null) {
                        return rangeNumberString;
                    }

                    // date
                    String rangeDateString = rangeDate(upperDateMinValue, currentDateValue, upperDateMaxValue, upperDateValue);
                    if (rangeDateString != null) {
                        return rangeDateString;
                    }

                    return null;
                })
                .filter(Objects::nonNull)
                .map(message -> String.format("%s: %s", upperCharacteristic.getBusinessKey(), message))
                .collect(Collectors.toList());
    }

    private String rangeDecimal(BigDecimal minValue, BigDecimal maxValue, BigDecimal currentValue, BigDecimal upperValue) {
        if (minValue != null && (currentValue == null || currentValue.compareTo(minValue) < 0)) {
            return String.format(compareDecimalValueAndMinValue,
                    currentValue,
                    minValue
            );
        }

        if (maxValue != null && (currentValue == null || currentValue.compareTo(maxValue) > 0)) {
            return String.format(compareDecimalValueAndMaxValue,
                    currentValue,
                    maxValue
            );
        }

        if (upperValue != null && (!upperValue.equals(currentValue))) {
            return String.format("decimal value [%s] is not equals to parent characteristic value [%s]",
                    currentValue,
                    upperValue
            );
        }
        return null;
    }

    private String rangeNumber(Integer upperNumberMinValue, Integer upperNumberMaxValue, Integer currentNumberValue, Integer upperNumberValue) {
        if (upperNumberMinValue != null && (currentNumberValue == null || currentNumberValue < upperNumberMinValue)) {
            return String.format(compareNumberValueAndMinValue,
                    currentNumberValue,
                    upperNumberMinValue
            );
        }

        if (upperNumberMaxValue != null && (currentNumberValue == null || currentNumberValue > upperNumberMaxValue)) {
            return String.format(compareNumberValueAndMaxValue,
                    currentNumberValue,
                    upperNumberMaxValue
            );
        }

        if (upperNumberValue != null && (!upperNumberValue.equals(currentNumberValue))) {
            return String.format("number value [%s] is not equals to parent characteristic value [%s]",
                    currentNumberValue,
                    upperNumberValue
            );
        }
        return null;
    }

    private String rangeDate(LocalDateTime upperDateMinValue, LocalDateTime currentDateValue, LocalDateTime upperDateMaxValue, LocalDateTime upperDateValue) {
        if (upperDateMinValue != null && (currentDateValue == null || currentDateValue.compareTo(upperDateMinValue) < 0)) {
            return String.format(compareDateValueAndMinValue,
                    currentDateValue,
                    upperDateMinValue
            );
        }

        if (upperDateMaxValue != null && (currentDateValue == null || currentDateValue.compareTo(upperDateMaxValue) > 0)) {
            return String.format("date value [%s] is greater than parent characteristic min value [%s]",
                    currentDateValue,
                    upperDateMaxValue
            );
        }

        if (upperDateValue != null && (!upperDateValue.equals(currentDateValue))) {
            return String.format("date value [%s] is not equals to parent characteristic value [%s]",
                    currentDateValue,
                    upperDateValue
            );
        }
        return null;
    }




















public List<String> validateProduct(CommonCharacteristic upperCharacteristic, Value value) {
        return upperCharacteristic.getValues().stream()
                .map(upperCharacteristicValue -> {
                    // *** Попадание точных значений внутрь диапазонов
                    // decimal
                    if (upperCharacteristicValue.getDecimalMinValue() != null) {
                        if (value.getDecimalValue() == null || value.getDecimalValue().compareTo(upperCharacteristicValue.getDecimalMinValue()) < 0)
                            return String.format("decimal value [%s] is lesser than parent characteristic min value [%s]",
                                    value.getDecimalValue(),
                                    upperCharacteristicValue.getDecimalMinValue()
                            );
                    }

                    if (upperCharacteristicValue.getDecimalMaxValue() != null) {
                        if (value.getDecimalValue() == null || value.getDecimalValue().compareTo(upperCharacteristicValue.getDecimalMaxValue()) > 0)
                            return String.format("decimal value [%s] is greater than parent characteristic max value [%s]",
                                    value.getDecimalValue(),
                                    upperCharacteristicValue.getDecimalMaxValue()
                            );
                    }

                    if (upperCharacteristicValue.getDecimalValue() != null) {
                        if (!upperCharacteristicValue.getDecimalValue().equals(value.getDecimalValue()))
                            return String.format("decimal value [%s] is not equals to parent characteristic value [%s]",
                                    value.getDecimalValue(),
                                    upperCharacteristicValue.getDecimalValue()
                            );
                    }

                    // number
                    if (upperCharacteristicValue.getNumberMinValue() != null) {
                        if (value.getNumberValue() == null || value.getNumberValue() < upperCharacteristicValue.getNumberMinValue())
                            return String.format("number value [%s] is lesser than parent characteristic min value [%s]",
                                    value.getNumberValue(),
                                    upperCharacteristicValue.getNumberMinValue()
                            );
                    }

                    if (upperCharacteristicValue.getNumberMaxValue() != null) {
                        if (value.getNumberValue() == null || value.getNumberValue() > upperCharacteristicValue.getNumberMaxValue())
                            return String.format("number value [%s] is greater than parent characteristic max value [%s]",
                                    value.getNumberValue(),
                                    upperCharacteristicValue.getNumberMaxValue()
                            );
                    }

                    if (upperCharacteristicValue.getNumberValue() != null) {
                        if (!upperCharacteristicValue.getNumberValue().equals(value.getNumberValue()))
                            return String.format("number value [%s] is not equals to parent characteristic value [%s]",
                                    value.getNumberValue(),
                                    upperCharacteristicValue.getNumberValue()
                            );
                    }

                    // date
                    if (upperCharacteristicValue.getDateMinValue() != null) {
                        if (value.getDateValue() == null || value.getDateValue().compareTo(upperCharacteristicValue.getDateMinValue()) < 0)
                            return String.format("date value [%s] is lesser than parent characteristic min value [%s]",
                                    value.getDateValue(),
                                    upperCharacteristicValue.getDateMinValue()
                            );
                    }

                    if (upperCharacteristicValue.getDateMaxValue() != null) {
                        if (value.getDateValue() == null || value.getDateValue().compareTo(upperCharacteristicValue.getDateMaxValue()) > 0)
                            return String.format("date value [%s] is greater than parent characteristic min value [%s]",
                                    value.getDateValue(),
                                    upperCharacteristicValue.getDateMaxValue()
                            );
                    }

                    if (upperCharacteristicValue.getDateValue() != null) {
                        if (!upperCharacteristicValue.getDateValue().equals(value.getDateValue()))
                            return String.format("date value [%s] is not equals to parent characteristic value [%s]",
                                    value.getDateValue(),
                                    upperCharacteristicValue.getDateValue()
                            );
                    }

                    return null;
                })
                .filter(Objects::nonNull)
                .map(message -> String.format("%s: %s", upperCharacteristic.getBusinessKey(), message))
                .collect(Collectors.toList());
    }

