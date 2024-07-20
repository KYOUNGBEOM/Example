```java
package com.example.validation.annotation;

import com.example.validation.validator.PhoneNumberValidator;
import jakarta.validation.Constraint;
import jakarta.validation.Payload;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

// 어떤 클래스를 통해 유효성 검사를 할 것인지 설정
@Constraint(validatedBy = {PhoneNumberValidator.class})
//적용시킬 위치 설정
@Target( {ElementType.FIELD} )
// 언제 실행시킬건지 설정
@Retention(RetentionPolicy.RUNTIME)
public @interface PhoneNumber {

    String message() default "핸드폰 번호 양식에 맞지 않습니다. ex) 000-0000-0000";

    String regexp() default "^\\d{2,3}-\\d{3,4}-\\d{4}$";

    Class<?>[] groups() default {};

    Class<? extends Payload>[] payload() default {};
}
```

```java
package com.example.validation.validator;

import com.example.validation.annotation.PhoneNumber;
import jakarta.validation.ConstraintValidator;
import jakarta.validation.ConstraintValidatorContext;

import java.util.regex.Pattern;

// 어노테이션 유효성검사 설정하는 클래스
public class PhoneNumberValidator implements ConstraintValidator<PhoneNumber, String> {

    private String regexp;

    @Override
    public void initialize(PhoneNumber constraintAnnotation) {
        this.regexp = constraintAnnotation.regexp();
    }

    @Override
    public boolean isValid(String value, ConstraintValidatorContext context) {
        boolean result = Pattern.matches(regexp, value);
        return result;
    }
}
```

```java
package com.example.validation.annotation;

import com.example.validation.validator.YearMonthValidator;
import jakarta.validation.Constraint;
import jakarta.validation.Payload;
import jakarta.validation.constraints.NotBlank;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Constraint(validatedBy = YearMonthValidator.class)
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
@NotBlank
public @interface YearMonth{

    String message() default "날짜 양식에 맞지 않습니다 ex) 204207";

    String pattern() default "yyyyMM";

    Class<?>[] groups() default {};

    Class<? extends Payload>[] payload() default {};
}
```

```java
package com.example.validation.validator;

import com.example.validation.annotation.YearMonth;
import jakarta.validation.ConstraintValidator;
import jakarta.validation.ConstraintValidatorContext;

import java.time.LocalDate;
import java.time.format.DateTimeFormatter;


public class YearMonthValidator implements ConstraintValidator<YearMonth, String> {

    private String pattern;

    @Override
    public void initialize(YearMonth constraintAnnotation) {
        this.pattern = constraintAnnotation.pattern();
    }

    @Override
    public boolean isValid(String value, ConstraintValidatorContext constraintValidatorContext) {

        var reValue = value+"01";
        var rePattern = pattern+"dd";

        try {
            LocalDate date = LocalDate.parse(reValue, DateTimeFormatter.ofPattern(rePattern));
            System.out.println(date);
            return true;
        }catch(Exception e) {
            return false;
        }
    }
}
```
