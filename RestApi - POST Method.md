```java
package com.example.rest_api.controller;

import model.BookRequest;
import model.UserRequest;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/api")
public class PostApiController {

    // return 타입이 String 일때는 Content-Type이 UTF-8이고, 객체일 때는 Json
    @PostMapping("/post")
    public BookRequest post(
        // post 또는 put 방식에서 http body로 들어오는 데이터를 @RequestBody 어노테이션이 선언된 객체에 매핑
        @RequestBody BookRequest bookRequest
    ) {
        System.out.println(bookRequest);
        return bookRequest;
    }

    @PostMapping("/user")
    public UserRequest user(
        @RequestBody UserRequest userRequest
    ) {
        System.out.println(userRequest);
        return userRequest;
    }
}
```
```java
package model;

import com.fasterxml.jackson.databind.PropertyNamingStrategies;
import com.fasterxml.jackson.databind.annotation.JsonNaming;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@AllArgsConstructor
@NoArgsConstructor
// 해당 클래스의 변수들은 스네이크 케이스로 매핑
@JsonNaming(value = PropertyNamingStrategies.SnakeCaseStrategy.class)
// user 메서드에서 사용되는 객체의 클래스
public class UserRequest {
    private String userName;

    private int userAge;

    private String email;
}
```
