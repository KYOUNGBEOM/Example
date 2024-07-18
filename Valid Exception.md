# common code
```java
package com.example.validation.model;

import com.fasterxml.jackson.databind.PropertyNamingStrategies;
import com.fasterxml.jackson.databind.annotation.JsonNaming;
import jakarta.validation.Valid;
import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;

import java.util.List;

@Data
@AllArgsConstructor
@NoArgsConstructor
@Builder
@JsonNaming(PropertyNamingStrategies.SnakeCaseStrategy.class)
public class Api<T> {

    private String resultCode;

    private String resultMessage;

    @Valid
    private T data;

    private Error error;

    @Data
    @AllArgsConstructor
    @NoArgsConstructor
    @Builder
    @JsonNaming(PropertyNamingStrategies.SnakeCaseStrategy.class)
    public static class Error {
        private List< String> errorMessage;
    }
}
```
```java
package com.example.validation.model;

import com.fasterxml.jackson.databind.PropertyNamingStrategies;
import com.fasterxml.jackson.databind.annotation.JsonNaming;
import jakarta.validation.constraints.*;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

import java.time.LocalDateTime;

@Data
@AllArgsConstructor
@NoArgsConstructor
@JsonNaming(PropertyNamingStrategies.SnakeCaseStrategy.class)
public class UserRegisterRequest {

    // @NotNull // != null
    // @NotEmpty // != null && != ""
    @NotBlank // != null && != "" && != " "
    private String name;

    @Size(min = 1, max = 12) // 1 ~ 12
    @NotBlank
    private String password;

    @NotNull
    @Min(1)
    @Max(100)
    private Integer age;

    @Email
    private String email;

    // 휴대폰 번호는 별도 어노테이션이 없음
    @Pattern(regexp = "^\\d{2,3}-\\d{3,4}-\\d{4}$", message = "휴대폰 번호 형식이 올바르지 않습니다.")
    private String phoneNumber;

    @FutureOrPresent // 현재 또는 미래
    private LocalDateTime registerAt;
}
```
# Using BindingResult
```java
package com.example.validation.controller;

import com.example.validation.model.Api;
import com.example.validation.model.UserRegisterRequest;
import jakarta.validation.Valid;
import lombok.extern.slf4j.Slf4j;
import org.springframework.http.HttpStatus;
import org.springframework.validation.BindingResult;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.stream.Collectors;

@Slf4j
@RestController
@RequestMapping("/api/user")
public class UserApiController {

    @PostMapping("")
    public Api<? extends Object> register(
            @Valid // 요청이 들어올때 해당 클래스에 붙어 있는 어노테이션을 기반으로 유효성 검사
            @RequestBody Api<UserRegisterRequest> userRegisterRequest,

            // @Valid 어노테이션을 통한 유효성 검사중 에러 발생시 bindingResult 객체에 담김
            BindingResult bindingResult
    ) {
            log.info("init : {}", userRegisterRequest);

            if(bindingResult.hasErrors()) {
                var errorMessageList = bindingResult.getFieldErrors().stream()
                        // map 함수는 값을 변환할 떄 사용
                        .map ( it -> {
                            var format = "%s : { %s } 은 %s";
                            var message = String.format(format, it.getField(), it.getRejectedValue(), it.getDefaultMessage());
                            return message;
                        }).collect(Collectors.toList());

                var error = Api.Error
                        .builder()
                        .errorMessage(errorMessageList)
                        .build();

                var errorResponse = Api
                        .builder()
                        .resultCode(String.valueOf(HttpStatus.BAD_REQUEST.value()))
                        .resultMessage(HttpStatus.BAD_REQUEST.name())
                        .error(error)
                        .build();

                return errorResponse;
            }

            var body = userRegisterRequest.getData();
            Api<UserRegisterRequest> response = Api.<UserRegisterRequest>builder()
                    .resultCode(String.valueOf(HttpStatus.OK.value()))
                    .resultMessage(HttpStatus.OK.name())
                    .data(body)
                    .build();

            return response;
    }
}
```

# Using Exception Handler
```java
package com.example.validation.controller;

import com.example.validation.model.Api;
import com.example.validation.model.UserRegisterRequest;
import jakarta.validation.Valid;
import lombok.extern.slf4j.Slf4j;
import org.springframework.http.HttpStatus;
import org.springframework.validation.BindingResult;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.stream.Collectors;

@Slf4j
@RestController
@RequestMapping("/api/user")
public class UserApiController {

    @PostMapping("")
    public Api<UserRegisterRequest> register(
            @Valid // 요청이 들어올때 해당 클래스에 붙어 있는 어노테이션을 기반으로 유효성 검사
            @RequestBody Api<UserRegisterRequest> userRegisterRequest
    ) {
            log.info("init : {}", userRegisterRequest);

            var body = userRegisterRequest.getData();
            Api<UserRegisterRequest> response = Api.<UserRegisterRequest>builder()
                    .resultCode(String.valueOf(HttpStatus.OK.value()))
                    .resultMessage(HttpStatus.OK.name())
                    .data(body)
                    .build();

            return response;
    }
}
```

```java
package com.example.validation.exception;

import com.example.validation.model.Api;
import lombok.extern.slf4j.Slf4j;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

import java.util.stream.Collectors;

@Slf4j
@RestControllerAdvice
public class ValidExceptionHandler {

    @ExceptionHandler(value = { MethodArgumentNotValidException.class})
    public ResponseEntity<Api> validationException(
            MethodArgumentNotValidException e
    ) {
            log.error("MethodArgumentNotValidException : {}", e.getMessage(), e);

            var errorMessageList = e.getFieldErrors().stream()
                    // map 함수는 값을 변환할 떄 사용
                    .map ( it -> {
                        var format = "%s : { %s } 은 %s";
                        var message = String.format(format, it.getField(), it.getRejectedValue(), it.getDefaultMessage());
                        return message;
                    }).collect(Collectors.toList());

            var error = Api.Error
                    .builder()
                    .errorMessage(errorMessageList)
                    .build();

            var errorResponse = Api
                    .builder()
                    .resultCode(String.valueOf(HttpStatus.BAD_REQUEST.value()))
                    .resultMessage(HttpStatus.BAD_REQUEST.name())
                    .error(error)
                    .build();

            return ResponseEntity
                    .status(HttpStatus.BAD_REQUEST)
                    .body(errorResponse);
    }
}
```
