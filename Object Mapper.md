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
