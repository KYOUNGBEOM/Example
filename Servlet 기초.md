```java
package web.controller;

import java.io.IOException;
import java.io.PrintWriter;

import jakarta.servlet.ServletException;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

public class MyServlet extends HttpServlet {
	private static final long serialVersionUID = 1L;
       
   	 public MyServlet() {
        	super();
    	}

	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		response.setContentType("text/html; charset=UTF-8"); // 한글 처리
		PrintWriter out = response.getWriter(); // response 로부터 printerWriter 객체를 가져와 서블릿이 클라이언트(웹브라우저)로 데이터 전송 	
		out.println("doGet 호출됨");
	}

	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		response.setContentType("text/html; charset=UTF-8"); // 한글 처리
		PrintWriter out = response.getWriter(); // response 로부터 printerWriter 객체를 가져와 서블릿이 클라이언트(웹브라우저)로 데이터 전송 
		out.println("doPost 호출됨");
	}

}
```
