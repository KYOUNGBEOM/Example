```java
package com.example.rest_api.controller;

import model.QueryParam;
import org.springframework.web.bind.annotation.*;

// rest-api를 처리하는 controller
@RestController
@RequestMapping("/api")
public class RestApiController {

    @GetMapping(path="/hello")
    public String hello() {
        var html = "<html><body><h1>Hello Spring boot</h1></body></html>";
        return html;
    }

    // Path variable
    @GetMapping(path = "/echo/{message}/age/{age}/is-man/{isMan}")
    public String echo(
            @PathVariable String message,
            @PathVariable int age,
            @PathVariable boolean isMan
    ) {
        System.out.println("echo message : " + message);
        System.out.println("echo age : " + age);
        System.out.println("echo isMan : " + isMan);

        String upperCase = message.toUpperCase();

        return upperCase;
    }

    // Query Parameter
    // http://localhost:8080/api/query-param?message=hello&issuedYear=2024&issued-month=07&issued_day=14
    @GetMapping(path = "/query-param")
    public void queryParam(
            @RequestParam String message,
            @RequestParam String issuedYear,
            @RequestParam(name="issued-month") String issuedMonth,
            @RequestParam(name="issued_day") String issuedDay
    ) {
        System.out.println(message);
        System.out.println(issuedYear);
        System.out.println(issuedMonth);
        System.out.println(issuedDay);
    }

    // Query Parameter
    // http://localhost:8080/api/query-param2?message=hello&issuedYear=2024&issuedMonth=07&issuedDay=14
    // 객체로 파싱
    @GetMapping(path = "/query-param2")
    public void queryParam2(
            QueryParam queryParam
    ) {
        System.out.println(queryParam);
    }
}
```

```java
// queryParam2 메서드에서 사용하는 객체의 클래스
package model;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
// 클래스내 변수들을 매개변수로 하는 생성자 생성
@AllArgsConstructor
// 매개변수가 없는 기본 생성자 생성
@NoArgsConstructor
public class QueryParam {
    private String message;

    private String issuedYear;

    private String issuedMonth;

    private String issuedDay;
}

```
