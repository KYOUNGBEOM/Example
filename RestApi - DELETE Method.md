```java
package com.example.rest_api.controller;

import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@Slf4j
@RestController
@RequestMapping("/api")
public class DeleteApiController {

    // path 속성을 사용해 주소 여러개 사용가능
    @DeleteMapping(path = {
            "/user/{userName}/delete",
            "/user/{userName}/del"}
    )
    public void delete(
        @PathVariable String userName
    ) {
        log.info("user-name : {}", userName);
    }
}
```
