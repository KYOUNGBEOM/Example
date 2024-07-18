```java
package com.example.exception.model;

import com.fasterxml.jackson.databind.PropertyNamingStrategies;
import com.fasterxml.jackson.databind.annotation.JsonNaming;
import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@AllArgsConstructor
@NoArgsConstructor
@Builder
@JsonNaming(value = PropertyNamingStrategies.SnakeCaseStrategy.class)
public class Api<T> {

    private String resultCode;

    private String resultMessage;

    private T data;
}
```

```java
package com.example.exception.model;

import com.fasterxml.jackson.databind.PropertyNamingStrategies;
import com.fasterxml.jackson.databind.annotation.JsonNaming;
import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@NoArgsConstructor
@AllArgsConstructor
@Builder
@JsonNaming(value = PropertyNamingStrategies.SnakeCaseStrategy.class)
public class UserResponse {

    private String id;

    private String name;

    private Integer age;
}
```

```java
package com.example.exception.controller;

import com.example.exception.model.Api;
import com.example.exception.model.UserResponse;
import org.springframework.http.HttpStatus;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;

@RestController
@RequestMapping("/api/user")
public class UserApiController {

    private static List<UserResponse> userList = List.of(
            UserResponse.builder()
                    .id("1")
                    .age(10)
                    .name("홍길동")
                    .build(),

            UserResponse.builder()
                    .id("2")
                    .age(10)
                    .name("유관순")
                    .build()
    );

    @GetMapping("/id/{userId}")
    public Api<UserResponse> getUser(
            @PathVariable String userId
    ){
            var user = userList.stream()
                .filter(
                    it -> it.getId().equals(userId)
                )
                    .findFirst()
                    .get();

            Api<UserResponse> response = Api.<UserResponse>builder()
                    .resultCode(String.valueOf(HttpStatus.OK.value()))
                    .resultMessage(HttpStatus.OK.name())
                    .data(user)
                    .build();

            return response;
    }
}
```
```java
package com.example.exception.exception;

import com.example.exception.model.Api;
import lombok.extern.slf4j.Slf4j;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

import java.util.NoSuchElementException;

@Slf4j
// @RestController가 사용되는 곳에서 발생하는 예외를 감지
@RestControllerAdvice
public class RestApiExceptionHandler {
    @ExceptionHandler(value = {NoSuchElementException.class})
    public ResponseEntity<Api> NoSuchElementException(
            NoSuchElementException e
    ) {
        log.error("", e);

        var response = Api.builder()
                .resultCode(String.valueOf(HttpStatus.BAD_REQUEST.value()))
                .resultMessage(HttpStatus.BAD_REQUEST.name())
                .build();

        return ResponseEntity
                .status(HttpStatus.NOT_FOUND)
                .body(response);
    }
 }
```

    만약 예외처리하는 클래스가 여러개 있는 경우, 
    클래스간의 처리 순서를 지정해주려면 @Order(value =  ) 어노테이션 사용
    
    default 값은 Integer.MAX_VALUE 이고, 값이 클 수록 뒤에 실행
