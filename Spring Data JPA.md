  Docker에 3306 포트로 MySQLDB를 연결해둔 상태

# docker-compose.yaml
```java
version: "3"
services:
  db:
    image: mysql:8.0.26
    restart: always
    command:
      - --lower_case_table_names=1
      - --character-set-server=utf8mb4
      - --collation-server=utf8mb4_unicode_ci
    container_name: mysql
    ports:
      - "3306:3306"
    environment:
      - MYSQL_DATABASE=mydb
      - MYSQL_ROOT_PASSWORD=root1234!!
      - TZ=Asia/Seoul
    volumes:
      - C:\MYSQL:/var/lib/mysql
```

# application.yaml
```java
spring:
  jpa:
    show-sql: true
    properties:
      format_sql: true
      dialect: org.hibernate.dialect.MySQL8Dialect
    hibernate:
      ddl-auto: validate
  datasource:
    url: jdbc:mysql://localhost:3306/user?useSSL=false&useUnicode=true&allowPublicKeyRetrieval=true
    driver-class-name: com.mysql.cj.jdbc.Driver
    username: root
    password: root1234!!
```

# UserEntity
```java
package com.example.jpa.user.db;

import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;

// 데이터베이스 오브젝트와 데이터베이스 칼럼은 매핑 시켜주는 역할을 하는것이 ORM

@Data
@AllArgsConstructor
@NoArgsConstructor
@Builder
// 데이터베이스와 매핑시킬 테이블 설정
@Entity(name = "user")
public class UserEntity {

    @Id
    // 사용하는 데이터베이스마다 GenerationType이 달라짐
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private Integer age;
    private String email;
}
```

# UserRepository
```java
package com.example.jpa.user.db;

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Service;

@Service
public interface UserRepository extends JpaRepository<UserEntity, Long> {
}
```

# UserApiController  
```java
package com.example.jpa.user.controller;

import com.example.jpa.user.db.UserEntity;
import com.example.jpa.user.db.UserRepository;
import lombok.RequiredArgsConstructor;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

import java.util.List;

@RequiredArgsConstructor
@RestController
@RequestMapping("/api/user")
public class UserApiController {

    private final UserRepository userRepository;

    @GetMapping("/find-all")
    public List<UserEntity> findAll() {
        return userRepository.findAll();
    }

    @GetMapping("/name")
    public void autoSave(
            @RequestParam(value = "name") String name
    ) {
        var user = UserEntity.builder()
                        .name(name)
                        .build();

        userRepository.save(user);
    }
}
```
