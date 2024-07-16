```java
package com.example.rest_api;

import com.example.rest_api.model.UserRequest;
import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
class RestApiApplicationTests {

	@Autowired
	private ObjectMapper objectMapper;

	@Test
	void contextLoads() throws JsonProcessingException {
		var user = new UserRequest();
		user.setUserName("홍길동");
		user.setUserAge(10);
		user.setEmail("hong@gamil.com");
		user.setIsKorean(true);

		// UserRequest 객체를 json 데이터로 변환 (직렬화)
		var json = objectMapper.writeValueAsString(user);
		System.out.println(json);

		// json 데이터를 UserRequest에 매핑 (역직렬화)
		var dto = objectMapper.readValue(json, UserRequest.class);
		System.out.println(dto);
	}
}
```

	1. Object Mapper는 직렬화를 할때 변수에 매칭되는 것이 아니라, get 메서드에 매핑됨
	   반면 역직렬화를 할때는 기본적으로 set 메서드에 매핑되고, set이 없다면 get 메서드에 매핑
 	
  	2. get메서드를 매핑하고 싶지 않으면 @JsonIgnore를 사용해야함

   	3. 만약 직렬화되는 변수명을 변경하고 싶거나 Json데이터 변수명과 클래스의 변수명이 맞지 않을경 @JsonProperty("") 사용

```java
package com.example.rest_api;

import com.example.rest_api.model.UserRequest;
import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
class RestApiApplicationTests {

	@Autowired
	private ObjectMapper objectMapper;

	@Test
	void contextLoads() throws JsonProcessingException {
		var user = new UserRequest("홍길동", 10, "hong@gmail.com", true);

		// UserRequest 객체를 json 데이터로 변환 (직렬화)
		var json = objectMapper.writeValueAsString(user);
		System.out.println(json);

		// json 데이터를 UserRequest에 매핑 (역직렬화)
		var dto = objectMapper.readValue(json, UserRequest.class);
		System.out.println(dto);
	}
}    
```
```java
package com.example.rest_api.model;

import com.fasterxml.jackson.annotation.JsonIgnore;
import com.fasterxml.jackson.annotation.JsonProperty;
import com.fasterxml.jackson.databind.PropertyNamingStrategies;
import com.fasterxml.jackson.databind.annotation.JsonNaming;
import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;

@AllArgsConstructor
@NoArgsConstructor
// 해당 클래스의 변수들은 스네이크 케이스로 매핑
@JsonNaming(value = PropertyNamingStrategies.SnakeCaseStrategy.class)
public class UserRequest {
    private String userName;

    private int userAge;

    @JsonProperty("user_email")
    private String email;

    // boolean 타입으로 설정시 setKorean(), Boolean 타입으로 설정시 setIsKorean()
    private Boolean isKorean;

    public String getUserName() {
        return userName;
    }

    public int getUserAge() {
        return userAge;
    }

    public String getEmail() {
        return email;
    }

    @JsonIgnore
    public String getName() {
        return userName;
    }

    public Boolean getIsKorean() {
        return isKorean;
    }

    @Override
    public String toString() {
        return "UserRequest{" +
                "userName='" + userName + '\'' +
                ", userAge=" + userAge +
                ", email='" + email + '\'' +
                ", isKorean=" + isKorean +
                '}';
    }
}
```
![image](https://github.com/user-attachments/assets/bf4d2842-dce2-4ce7-8b6e-57a13932bff1)
