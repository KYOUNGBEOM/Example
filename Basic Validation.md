[Validation Spec](https://beanvalidation.org/2.0/spec/)

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

```java
package com.example.validation.controller;

import com.example.validation.model.UserRegisterRequest;
import jakarta.validation.Valid;
import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@Slf4j
@RestController
@RequestMapping("/api/user")
public class UserApiController {

    @PostMapping("")
    public UserRegisterRequest register(
            @Valid // 요청이 들어올때 해당 클래스에 붙어 있는 어노테이션을 기반으로 유효성 검사
            @RequestBody UserRegisterRequest userRegisterRequest
    ) {
            log.info("init : {}", userRegisterRequest);

            return userRegisterRequest;
    }
}
```
