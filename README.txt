package com.farzoom.factoring.catalog.service.inner;

import com.farzoom.factoring.catalog.enums.EntityTypeEnum;
import com.farzoom.factoring.catalog.model.Value;
import com.farzoom.factoring.catalog.model.common.CommonCharacteristic;
import com.google.common.base.Strings;
import lombok.var;
import org.springframework.stereotype.Service;

import java.math.BigDecimal;
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;
import java.util.Objects;
import java.util.stream.Collectors;

@Service
public class ValueValidator {

    private static String compareNumberValueAndMaxValue = "number value [%s] is greater than parent characteristic max value [%s]";
    private static String compareNumberValueAndMinValue = "number value [%s] is lesser than parent characteristic min value [%s]";
    private static String compareDecimalValueAndMinValue = "decimal value [%s] is lesser than parent characteristic min value [%s]";
    private static String compareDecimalValueAndMaxValue = "decimal value [%s] is greater than parent characteristic max value [%s]";
    private static String compareDateValueAndMinValue = "date value [%s] is lesser than parent characteristic min value [%s]";

    public List<String> validate(EntityTypeEnum entityType, CommonCharacteristic upperCharacteristic, Value value, String charKey) {
        switch (entityType) {
            case SPECIFICATION:
            case OFFERING:
            case OPERATION:
            case PRODUCT:
            default:
                return validate(upperCharacteristic, value, charKey);
        }
    }

    public List<String> validateProduct(CommonCharacteristic upperCharacteristic, Value value) {
        return upperCharacteristic.getValues().stream()
                .map(upperCharacteristicValue -> {
                    var minValue = upperCharacteristicValue.getDecimalMinValue();
                    var currentValue = value.getDecimalValue();
                    var currentDateValue = value.getDateValue();
                    var currentNumberValue= value.getNumberValue();
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

                    // number
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

                    // date
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
                })
                .filter(Objects::nonNull)
                .map(message -> String.format("%s: %s", upperCharacteristic.getBusinessKey(), message))
                .collect(Collectors.toList());
    }

    public List<String> validate(CommonCharacteristic upperCharacteristic, Value value, String charKey) {
        final List<String> result = new ArrayList<>();
        final boolean checkingValueFromCharacteristicLink = upperCharacteristic != null;

        if (Strings.isNullOrEmpty(value.getName()))
            value.setName(checkingValueFromCharacteristicLink ? upperCharacteristic.getBusinessKey() : "");

        if (value.getTextValue() != null && checkingValueFromCharacteristicLink &&
                upperCharacteristic.getValues() != null && !upperCharacteristic.getValues().isEmpty()) {
            String entityValueTextValue = value.getTextValue();
            boolean existsInUpperCharacteristic = upperCharacteristic.getValues()
                    .stream().anyMatch(upperCharacteristicValue -> upperCharacteristicValue.getTextValue().equals(entityValueTextValue));
            if (!existsInUpperCharacteristic) {
                result.add(String.format("%s: missing value similar to [%s] by name [%s] in Characteristic by charKey - [%s]", value.getName(), entityValueTextValue, value.getName(), charKey));
            }
        }

        // *** Либо min/max, либо фиксированное значение
        if ((value.getDecimalMinValue() != null || value.getDecimalMaxValue() != null) && value.getDecimalValue() != null)
            result.add(String.format("%s: decimal min/max or fix value should be set", value.getName()));

        if ((value.getNumberMinValue() != null || value.getNumberMaxValue() != null) && value.getNumberValue() != null)
            result.add(String.format("%s: number min/max or fix value should be set", value.getName()));

        if ((value.getDateMinValue() != null || value.getDateMaxValue() != null) && value.getDateValue() != null)
            result.add(String.format("%s: date min/max or fix value should be set", value.getName()));

        // *** min должен быть меньше max, если нет фиксированного значения
        if (value.getDecimalValue() == null && value.getDecimalMinValue() != null &&
                (value.getDecimalMaxValue() != null && value.getDecimalMinValue().compareTo(value.getDecimalMaxValue()) > 0))
            result.add(String.format("%s: decimal min value [%s] is greater than max value [%s]",
                    value.getName(),
                    value.getDecimalMinValue(),
                    value.getDecimalMaxValue()
            ));

        if (value.getNumberValue() == null && value.getNumberMinValue() != null &&
                (value.getNumberMaxValue() != null && value.getNumberMinValue().compareTo(value.getNumberMaxValue()) > 0))
            result.add(String.format("%s: number min value [%s] is greater than max value [%s]",
                    value.getName(),
                    value.getNumberMinValue(),
                    value.getNumberMaxValue()
            ));

        if (value.getDateValue() == null && value.getDateMinValue() != null &&
                (value.getDateMaxValue() != null && value.getDateMinValue().compareTo(value.getDateMaxValue()) > 0))
            result.add(String.format("%s: date min value [%s] is greater than max value [%s]",
                    value.getName(),
                    value.getDateMinValue(),
                    value.getDateMaxValue()
            ));

        if (checkingValueFromCharacteristicLink) {
            result.addAll(checkValueByUpperCharacteristic(upperCharacteristic, value));
        }

        return result;
    }

    private List<String> checkValueByUpperCharacteristic(CommonCharacteristic upperCharacteristic, Value value) {
        List<String> validateMessageList = upperCharacteristic.getValues().stream()
                .filter(t -> Objects.equals(t.getName(), value.getName()) || upperCharacteristic.getValues().size() == 1)
                .map(upperValue -> {
                    var upperDecimalMinValue = upperValue.getDecimalMinValue();
                    var valueDecimalMinValue = value.getDecimalMinValue();

                    // *** Попадание внутрь диапазонов
                    // decimal
                    if (upperDecimalMinValue != null) {
                        if ((valueDecimalMinValue == null && value.getDecimalValue() == null)
                                || (valueDecimalMinValue != null && valueDecimalMinValue.compareTo(upperDecimalMinValue) < 0)) {
                            return String.format(compareDecimalValueAndMinValue,
                                    valueDecimalMinValue,
                                    upperDecimalMinValue
                            );
                        }
                        if (value.getDecimalValue() != null && value.getDecimalValue().compareTo(upperDecimalMinValue) < 0) {
                            return String.format(compareDecimalValueAndMinValue,
                                    value.getDecimalValue(),
                                    upperDecimalMinValue
                            );
                        }
                    }

                    if (upperValue.getDecimalMaxValue() != null) {
                        if ((value.getDecimalMaxValue() == null && value.getDecimalValue() == null)
                                || (value.getDecimalMaxValue() != null && value.getDecimalMaxValue().compareTo(upperValue.getDecimalMaxValue()) > 0)) {
                            return String.format(compareDecimalValueAndMaxValue,
                                    value.getDecimalMaxValue(),
                                    upperValue.getDecimalMaxValue()
                            );
                        }
                        if (value.getDecimalValue() != null && value.getDecimalValue().compareTo(upperValue.getDecimalMaxValue()) > 0) {
                            return String.format(compareDecimalValueAndMaxValue,
                                    value.getDecimalValue(),
                                    upperValue.getDecimalMaxValue()
                            );
                        }
                    }

                    if (upperValue.getDecimalValue() != null && (!upperValue.getDecimalValue().equals(value.getDecimalValue()))) {
                        return String.format("decimal value [%s] is not equals to parent characteristic value [%s]",
                                value.getDecimalValue(),
                                upperValue.getDecimalValue()
                        );
                    }

                    // number
                    if (upperValue.getNumberMinValue() != null) {
                        if ((value.getNumberMinValue() == null && value.getNumberValue() == null)
                                || (value.getNumberMinValue() != null && value.getNumberMinValue() < upperValue.getNumberMinValue())) {
                            return String.format(compareNumberValueAndMinValue,
                                    value.getNumberMinValue(),
                                    upperValue.getNumberMinValue()
                            );
                        }
                        if (value.getNumberValue() != null && value.getNumberValue() < upperValue.getNumberMinValue()) {
                            return String.format(compareNumberValueAndMinValue,
                                    value.getNumberValue(),
                                    upperValue.getNumberMinValue()
                            );
                        }
                    }

                    if (upperValue.getNumberMaxValue() != null) {
                        if ((value.getNumberMaxValue() == null && value.getNumberValue() == null)
                                || (value.getNumberMaxValue() != null && value.getNumberMaxValue() > upperValue.getNumberMaxValue())) {
                            return String.format(compareNumberValueAndMaxValue,
                                    value.getNumberMaxValue(),
                                    upperValue.getNumberMaxValue()
                            );
                        }
                        if (value.getNumberValue() != null && value.getNumberValue() > upperValue.getNumberMaxValue()) {
                            return String.format(compareNumberValueAndMaxValue,
                                    value.getNumberValue(),
                                    upperValue.getNumberMaxValue()
                            );
                        }
                    }

                    if ((upperValue.getNumberValue() != null) && (!upperValue.getNumberValue().equals(value.getNumberValue()))){
                            return String.format("number value [%s] is not equals to parent characteristic value [%s]",
                                    value.getNumberValue(),
                                    upperValue.getNumberValue()
                            );
                    }

                    // date
                    if (upperValue.getDateMinValue() != null) {
                        if (value.getDateMinValue() == null || value.getDateMinValue().isBefore(upperValue.getDateMinValue())) {
                            return String.format(compareDateValueAndMinValue,
                                    value.getDateMinValue(),
                                    upperValue.getDateMinValue()
                            );
                        }
                        if (value.getDateValue() != null && value.getDateValue().isBefore(upperValue.getDateMinValue())) {
                            return String.format(compareDateValueAndMinValue,
                                    value.getDateValue(),
                                    upperValue.getDateMinValue()
                            );
                        }
                    }

                    if (upperValue.getDateMaxValue() != null) {
                        if (value.getDateMaxValue() == null || value.getDateMaxValue().isAfter(upperValue.getDateMaxValue())) {
                            return String.format("date value [%s] is greater than parent characteristic max value [%s]",
                                    value.getDateMaxValue(),
                                    upperValue.getDateMaxValue()
                            );
                        }
                        if (value.getDateValue() != null && value.getDateValue().isAfter(upperValue.getDateMaxValue())) {
                            return String.format("date value [%s] is greater than parent characteristic max value [%s]",
                                    value.getDateValue(),
                                    upperValue.getDateMaxValue()
                            );
                        }
                    }

                    if ((upperValue.getDateValue() != null) && (!upperValue.getDateValue().equals(value.getDateValue()))){
                            return String.format("date value [%s] is not equals to parent characteristic value [%s]",
                                    value.getDateValue(),
                                    upperValue.getDateValue()
                            );
                    }

                    return null;
                })
                .collect(Collectors.toList());

        if (validateMessageList.stream().anyMatch(Objects::isNull)) {
            return Collections.emptyList();
        }

        return validateMessageList.stream()
                .filter(Objects::nonNull)
                .map(message -> String.format("%s: %s", upperCharacteristic.getBusinessKey(), message))
                .collect(Collectors.toList());
    }
}
