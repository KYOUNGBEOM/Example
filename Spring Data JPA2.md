# UserEntity
```java
package com.example.demo.user.model;

import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Data;
import lombok.NoArgsConstructor;

@Data
@NoArgsConstructor
@AllArgsConstructor
@Builder
@Entity(name = "user")
public class UserEntity {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    private int score;
}
```

# UserRepository
```java
package com.example.demo.user.db;

import com.example.demo.user.model.UserEntity;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.query.Param;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public interface UserRepository extends JpaRepository<UserEntity, Long> {

    // select * from where score >= ?
    List<UserEntity> findAllByScoreGreaterThanEqual(int score);

    // select * from user where score >= ? and score <= ?
    List<UserEntity> findAllByScoreGreaterThanEqualAndScoreLessThan(int min, int max);

    // 직접 네이티브 쿼리를 날리는법
    @Query(
            value = "select * from user as u where u.score >= ?1 AND u.score <= ?2",
            nativeQuery = true
    )
    List<UserEntity> score(int min, int max);

    // param
    @Query(
            value = "select * from user as u where u.score >= :min AND u.score <= :max",
            nativeQuery = true
    )
    List<UserEntity> score2(
            @Param(value = min) int min,
            @Param(value = max) int max);

    // jpql
    // u는 *와 같음
    @Query(
            value = "select u from user u where u.score >= ?1 AND u.score <= ?2"
    )
    List<UserEntity> score3(int min, int max);
}
```

# UserService
```java
package com.example.demo.user.service;

import com.example.demo.user.db.UserRepository;
import com.example.demo.user.model.UserEntity;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;
import java.util.Optional;

// 빈의 영역임을 명시
@Service
// 생성자로부터 자동으로 주입 받음
//@RequiredArgsConstructor
public class UserService {

    // private final UserRepository userRepository;

    // Bean context에 있는 객체를 자동으로 주입
    // -> Bean Context에 객체 등록하는법 1. @Service 사용 / 2. @Configuration + @Bean 사용
    @Autowired
    private UserRepository userRepository;

    public UserEntity save(UserEntity user) {
        return userRepository.save(user);
    }

    public List<UserEntity> findAll() {
        return userRepository.findAll();
    }

    public void delete(Long id) {
        // userRepository.delete(id);
    }

    public Optional<UserEntity> findById(Long id) {
        return userRepository.findById(id);
    }

    public List<UserEntity> findAllByScoreGreaterThanEqual(int score) {
        return userRepository.findAllByScoreGreaterThanEqual(score);
    }

    public List<UserEntity> filterScore(int min, int max) {
        return userRepository.score(min, max);
    }
}
```

# UserApiController
```java
package com.example.demo.user.controller;

import com.example.demo.user.model.UserEntity;
import com.example.demo.user.service.UserService;
import lombok.RequiredArgsConstructor;
import org.springframework.web.bind.annotation.*;

import java.util.List;
import java.util.Optional;

@RestController
@RequestMapping("/api/user")
// 생성자메서드로부터 자동으로 주입받음
@RequiredArgsConstructor
public class UserApiController {

    private final UserService userService;

    @PutMapping("")
    public UserEntity create(
            @RequestBody UserEntity userEntity
    ) {
        return userService.save(userEntity);
    }

    @PostMapping("/all")
    public List<UserEntity> findAll() {
        return userService.findAll();

    }

    @DeleteMapping("/delete/{id}")
    public void delete(
            @PathVariable Long id
    ) {
        userService.delete(id);
    }

    @GetMapping("/find/id/{id}")
    public UserEntity findById (
            @PathVariable Long id) {
        var response = userService.findById(id);
        return response.orElse(null);
    }

    @GetMapping("/find/score")
    public List<UserEntity> findAllByScoreGreaterThanEqual(
            @RequestParam int score) {
       return userService.findAllByScoreGreaterThanEqual(score);
    }

    @GetMapping("/find/min-max")
    public List<UserEntity> filterScore(
            @RequestParam int min,
            @RequestParam int max
    ) {
        return userService.filterScore(min, max);
    }
}
```
