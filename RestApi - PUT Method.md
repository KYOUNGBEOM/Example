```java
package com.example.rest_api.controller;

import lombok.extern.slf4j.Slf4j;
import model.UserRequest;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@Slf4j
@RestController
@RequestMapping("/api")
public class PutApiController {

    // post와 put 매핑 모두 데이터가 없으면 생성하지만, put 매핑만이 데이터가 존재할 경우 데이터를 업데이트
    @PutMapping("/put")
    public void put(
            @RequestBody UserRequest userRequest
    ) {
            // 로그팩 자체적으로 버퍼를 가지고 있어 System.out.println()보다 효율이 좋음
            // 추가적으로 내가 원하는대로 정보들 커스텀도 가능
            log.info("Request : {}" + userRequest);
    }
}
```
```java
// put 메서드 매개변수로 사용
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
public class UserRequest {
    private String userName;

    private int userAge;

    private String email;

    // boolean 타입으로 설정시 setKorean(), Boolean 타입으로 설정시 setIsKorean()
    private Boolean isKorean;
}

```
