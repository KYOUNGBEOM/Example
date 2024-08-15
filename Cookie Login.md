[010. cookie.zip](https://github.com/user-attachments/files/16621895/010.cookie.zip)

    쿠키 설정 코드
```java
package com.example.cookie.service;

import com.example.cookie.db.UserRepository;
import com.example.cookie.model.LoginRequest;
import jakarta.servlet.http.Cookie;
import jakarta.servlet.http.HttpServletResponse;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    public void login(
            LoginRequest loginRequest,
            HttpServletResponse httpServletResponse
    ) {
        var id = loginRequest.getId();
        var password = loginRequest.getPassword();

        var optionalUser = userRepository.findByName(id);

        if(optionalUser.isPresent()) {
            var userDto = optionalUser.get();

            if(userDto.getPassword().equals(password)) {
                // cookie에 해당 정보를 저장
                var cookie = new Cookie("authorization-cookie", userDto.getId());
                cookie.setDomain("localhost"); // 특정 도메인에서만 사용가능하게
                cookie.setPath("/");
                cookie.setHttpOnly(true); // js에서 cookie 탈취 못하게 막는 보안코드 -> 필수!!
                cookie.setSecure(true); // Https에서만 쿠기가 사용되도록 설정 -> 마찬가지로 필수!!
                cookie.setMaxAge(-1); // -1은 연결된 동안만 사용 = 세션이 유지되는 동안만 사용

                httpServletResponse.addCookie(cookie);
            } else {
                throw new RuntimeException("Password Not Match");
            }
        } else {
            throw new RuntimeException("User Not Found");
        }

    }
}
```
