[09. session.zip](https://github.com/user-attachments/files/16599504/09.session.zip)
<br>

    세션 서버에 저장
```java
package com.example.session.service;

import com.example.session.db.UserRepository;
import com.example.session.model.LoginRequest;
import jakarta.servlet.http.HttpSession;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    public void login(
            LoginRequest loginRequest,
            HttpSession httpSession
    ) {
        var id = loginRequest.getId();
        var pw = loginRequest.getPassword();

        var optionalUser = userRepository.findByName(id);

        if(optionalUser.isPresent()) {
            var userDto = optionalUser.get();

            if(userDto.getPassword().equals(pw)) {
                httpSession.setAttribute("USER", userDto);
            } else {
                throw new RuntimeException("Password Not Match");
            }
        }  else { // 없는 유저
            throw new RuntimeException("User Not Found");
        }
    }
}
```
<br>

    서버에서 클라이언트로 세션(쿠키) 내보냄
```java
package com.example.session.controller;

import com.example.session.model.UserDto;
import jakarta.servlet.http.HttpSession;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/api/user")
public class UserApiController {

    @GetMapping("/me")
    public UserDto me(
            HttpSession httpSession
    ) {
        var userObject = httpSession.getAttribute("USER");

        if(userObject != null) {
            var userDto = (UserDto) userObject;
            return userDto;
        } else {
            return null;
        }
    }

}
```
