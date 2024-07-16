```java
package com.example.exception.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;

@RestController
@RequestMapping("/api")
public class RestApiController {

    @GetMapping("")
    public void exception() {
        var list = List.of(1);

        // IndexOutOfBoundsException 발생
        System.out.println(list.get(1));

        throw new RuntimeException("run time exception call");
    }
}
```
```java
package com.example.exception.exception;

import lombok.extern.slf4j.Slf4j;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

@Slf4j
// @RestController가 사용되는 곳에서 발생하는 예외를 감지하고 처리
// basePackages = "", 속성 사용하여 특정 패키지에서 발생하는 예외만 처리 가능
// basePackageClasses = {}, 속성 사용하여 특정 클래스들에서 발생하는 예외만 처리 가능
@RestControllerAdvice
public class RestApiExceptionHandler {

    // 전역 예외처리
    @ExceptionHandler(value = {Exception.class})
    public ResponseEntity exception(
            Exception e
    ) {
        log.error("RestApiExceptionHandler", e);
        return ResponseEntity.status(200).build();
    }

    @ExceptionHandler(value = {IndexOutOfBoundsException.class})
    public ResponseEntity outOfBount(
        IndexOutOfBoundsException e
    ) {
        log.error("InderOutOfBoundsException ", e);
        return ResponseEntity.status(200).build();
    }
 }

```
