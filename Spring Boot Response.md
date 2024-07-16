```java
package com.example.rest_api.controller;

import com.example.rest_api.model.UserRequest;
import lombok.extern.slf4j.Slf4j;
import org.apache.catalina.User;
import org.springframework.http.HttpStatus;
import org.springframework.http.HttpStatusCode;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@Slf4j
// Controller와 RequestMapping이 세트 / RestController와 Get, Post, Put, Delete Mapping 세트
// Controoler를 사용했을때 Json타입으로 응답을 생성하려면 @ResponseBody 사용
@RestController
@RequestMapping("/api")
public class ResponseApiController {

    @GetMapping("/v1")
    public ResponseEntity user1(
    ) {
        var user = new UserRequest();
        user.setUserName("홍길동");
        user.setUserAge(10);
        user.setEmail("hong@gmail.com");

        log.info("user : {}", user);

        // response 커스텀 하는 코드
        var response = ResponseEntity
                .status(HttpStatus.BAD_REQUEST)
                .header("x-custom", "hi")
                .body(user);

        // json타입으로 자동 변환하여 response 보냄
        return response;
    }

    @GetMapping("/v2")
    public UserRequest user2(
    ) {
        var user = new UserRequest();
        user.setUserName("홍길동");
        user.setUserAge(10);
        user.setEmail("hong@gmail.com");

        log.info("user : {}", user);

        // json타입으로 자동 변환하여 response 보냄
        return user;
    }
}
```
