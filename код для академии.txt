import java.util.HashMap;
import java.util.Map;
import java.util.Scanner;

class Main {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        String input = scanner.nextLine();
        scanner.close();
        
        String result;
        try {
            result = calc(input);
            System.out.println(result);
        } catch (IllegalArgumentException e) {
            System.out.println("Ошибка: " + e.getMessage());
        }
    }

    public static String calc(String input) {
        String[] tokens = input.split(" ");

        if (tokens.length != 3) {
            throw new IllegalArgumentException("Некорректное выражение");
        }
        
        String operand1 = tokens[0];
        String operator = tokens[1];
        String operand2 = tokens[2];
        
        int num1;
        int num2;
        boolean isRoman = false;
        
        try {
            num1 = Integer.parseInt(operand1);
            num2 = Integer.parseInt(operand2);
        } catch (NumberFormatException e) {
            num1 = RomanNumeral.convertToInteger(operand1);
            num2 = RomanNumeral.convertToInteger(operand2);
            isRoman = true;
        }
        
        if (num1 < 1 || num1 > 10 || num2 < 1 || num2 > 10) {
            throw new IllegalArgumentException("Числа должны быть от 1 до 10 включительно");
        }
        
        int result;
        switch (operator) {
            case "+":
                result = num1 + num2;
                break;
            case "-":
                result = num1 - num2;
                break;
            case "*":
                result = num1 * num2;
                break;
            case "/":
                result = num1 / num2;
                break;
            default:
                throw new IllegalArgumentException("Некорректный оператор");
        }
        
        if (isRoman) {
            if (result < 1) {
                throw new IllegalArgumentException("Римские числа не могут быть меньше единицы");
            }
            return RomanNumeral.convertToRoman(result);
        } else {
            return String.valueOf(result);
        }
    }
}

class RomanNumeral {
    private static final Map<Character, Integer> ROMAN_TO_INTEGER = new HashMap<>();
    private static final Map<Integer, String> INTEGER_TO_ROMAN = new HashMap<>();
    
    static {
        ROMAN_TO_INTEGER.put('I', 1);
        ROMAN_TO_INTEGER.put('V', 5);
        ROMAN_TO_INTEGER.put('X', 10);
        ROMAN_TO_INTEGER.put('L', 50);
        ROMAN_TO_INTEGER.put('C', 100);
        ROMAN_TO_INTEGER.put('D', 500);
        ROMAN_TO_INTEGER.put('M', 1000);
        
        INTEGER_TO_ROMAN.put(1, "I");
        INTEGER_TO_ROMAN.put(4, "IV");
        INTEGER_TO_ROMAN.put(5, "V");
        INTEGER_TO_ROMAN.put(9, "IX");
        INTEGER_TO_ROMAN.put(10, "X");
        INTEGER_TO_ROMAN.put(40, "XL");
        INTEGER_TO_ROMAN.put(50, "L");
        INTEGER_TO_ROMAN.put(90, "XC");
        INTEGER_TO_ROMAN.put(100, "C");
        INTEGER_TO_ROMAN.put(400, "CD");
        INTEGER_TO_ROMAN.put(500, "D");
        INTEGER_TO_ROMAN.put(900, "CM");
        INTEGER_TO_ROMAN.put(1000, "M");
    }
    
    public static int convertToInteger(String roman) {
        int result = 0;
        int i = 0;
        
        while (i < roman.length()) {
            if (i < roman.length() - 1) {
                String twoChars = roman.substring(i, i + 2);
                if (INTEGER_TO_ROMAN.containsValue(twoChars)) {
                    result += getKeyByValue(INTEGER_TO_ROMAN, twoChars);
                    i += 2;
                    continue;
                }
            }
            
            char singleChar = roman.charAt(i);
            if (ROMAN_TO_INTEGER.containsKey(singleChar)) {
                result += ROMAN_TO_INTEGER.get(singleChar);
                i += 1;
            } else {
                throw new IllegalArgumentException("Некорректное римское число");
            }
        }
        
        return result;
    }
    
    public static String convertToRoman(int number) {
        if (number < 1) {
            throw new IllegalArgumentException("Римские числа не могут быть меньше единицы");
        }
        
        StringBuilder result = new StringBuilder();
        
        while (number > 0) {
            int maxVal = getMaxValue(number);
            result.append(INTEGER_TO_ROMAN.get(maxVal));
            number -= maxVal;
        }
        
        return result.toString();
    }
    
    private static int getMaxValue(int number) {
        for (int val : INTEGER_TO_ROMAN.keySet()) {
            if (val <= number) {
                return val;
            }
        }
        return 0;
    }
    
    private static <K, V> K getKeyByValue(Map<K, V> map, V value) {
        for (Map.Entry<K, V> entry : map.entrySet()) {
            if (value.equals(entry.getValue())) {
                return entry.getKey();
            }
        }
        return null;
    }
}