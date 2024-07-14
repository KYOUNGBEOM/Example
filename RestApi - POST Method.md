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
