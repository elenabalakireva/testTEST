public static List<Value> mergeValues(List<Value> values) {
        List<Value> result = new ArrayList<>();
        for (int i = 0; i < values.size(); i++) {
            Value value = values.get(i);
            String valueName = value.getName();
            boolean isDuplicate = false;
            for (int j = 0; j < result.size(); j++) {
                Value comparingValue = result.get(j);
                if (comparingValue.getName().equals(valueName)) {
                    if (checkIsRangeValue(value) && checkIsRangeValue(comparingValue)) {
                        Value mergedValue = mergeRangeValues(value, comparingValue);
                        if (mergedValue.getDecimalMinValue() != null || mergedValue.getDecimalMaxValue() != null) {
                            comparingValue.setDecimalMinValue(mergedValue.getDecimalMinValue());
                            comparingValue.setDecimalMaxValue(mergedValue.getDecimalMaxValue());
                        } else {
                            comparingValue.setNumberMinValue(mergedValue.getNumberMinValue());
                            comparingValue.setNumberMaxValue(mergedValue.getNumberMaxValue());
                        }
                    }
                    isDuplicate = true;
                    break;
                }
            }
            if (!isDuplicate) {
                // we need a copy of value in order not to corrupt the original data
                result.add(value.toBuilder().build());
            }
        }
        return result;
    }
