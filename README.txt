private static Value mergeRangeValues(Value value1, Value value2) {
    Value resultValue = new Value();
    
    mergeIntegerRangeValues(value1, value2, resultValue);
    mergeDecimalRangeValues(value1, value2, resultValue);
    
    return resultValue;
}

private static void mergeIntegerRangeValues(Value value1, Value value2, Value resultValue) {
    Integer numberMaxValue1 = value1.getNumberMaxValue();
    Integer numberMinValue1 = value1.getNumberMinValue();
    Integer numberMaxValue2 = value2.getNumberMaxValue();
    Integer numberMinValue2 = value2.getNumberMinValue();

    if (numberMinValue1 != null) {
        if (numberMinValue2 != null && numberMinValue1.compareTo(numberMinValue2) <= 0) {
            resultValue.setNumberMinValue(numberMinValue1);
        } else {
            resultValue.setNumberMinValue(numberMinValue2);
        }
    } else if (numberMinValue2 != null) {
        resultValue.setNumberMinValue(numberMinValue2);
    }
    
    if (numberMaxValue1 != null) {
        if (numberMaxValue2 != null && numberMaxValue1.compareTo(numberMaxValue2) > 0) {
            resultValue.setNumberMaxValue(numberMaxValue1);
        } else {
            resultValue.setNumberMaxValue(numberMaxValue2);
        }
    } else if (numberMaxValue2 != null) {
        resultValue.setNumberMaxValue(numberMaxValue2);
    }
}

private static void mergeDecimalRangeValues(Value value1, Value value2, Value resultValue) {
    BigDecimal decimalMinValue1 = value1.getDecimalMinValue();
    BigDecimal decimalMaxValue1 = value1.getDecimalMaxValue();
    BigDecimal decimalMinValue2 = value2.getDecimalMinValue();
    BigDecimal decimalMaxValue2 = value2.getDecimalMaxValue();

    if (decimalMinValue1 != null) {
        if (decimalMinValue2 != null && decimalMinValue1.compareTo(decimalMinValue2) <= 0) {
            resultValue.setDecimalMinValue(decimalMinValue1);
        } else {
            resultValue.setDecimalMinValue(decimalMinValue2);
        }
    } else if (decimalMinValue2 != null) {
        resultValue.setDecimalMinValue(decimalMinValue2);
    }
    
    if (decimalMaxValue1 != null) {
        if (decimalMaxValue2 != null && decimalMaxValue1.compareTo(decimalMaxValue2) > 0) {
            resultValue.setDecimalMaxValue(decimalMaxValue1);
        } else {
            resultValue.setDecimalMaxValue(decimalMaxValue2);
        }
    } else if (decimalMaxValue2 != null) {
        resultValue.setDecimalMaxValue(decimalMaxValue2);
    }
}
