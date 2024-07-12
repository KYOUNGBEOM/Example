```java
package web.controller;

import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.io.PrintWriter;
import java.net.HttpURLConnection;
import java.net.URL;

import org.json.JSONArray;
import org.json.JSONObject;

import jakarta.servlet.ServletException;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

public class MyServlet extends HttpServlet {
    private static final long serialVersionUID = 1L;
    private static String API_KEY = "API 키";
    private static String GPT_URL = "https://api.openai.com/v1/chat/completions";

    public MyServlet() {
        super();
    }

    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        response.setContentType("text/html; charset=UTF-8"); // 한글 처리
        PrintWriter out = response.getWriter(); // PrintWriter를 통해 서블릿이 클라이언트(웹브라우저)로 데이터 전송
        out.println("doGet 호출됨 <br>");

        gpt(request, out);
    }

    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        response.setContentType("text/html; charset=UTF-8"); // 한글 처리
        PrintWriter out = response.getWriter(); // response 로부터 printerWriter 객체를 가져와 서블릿이 클라이언트(웹브라우저)로 데이터 전송
        out.println("doPost 호출됨");
    }

    private void gpt(HttpServletRequest request, PrintWriter out) throws IOException {
        URL url = new URL(GPT_URL);
        HttpURLConnection connection = (HttpURLConnection) url.openConnection();

        connection.setRequestMethod("POST"); // 메서드 방식 POST 설정
        connection.setRequestProperty("Content-Type", "application/json"); // json 데이터 타입 설정
        connection.setRequestProperty("Authorization", "Bearer " + API_KEY);
        connection.setDoInput(true); // 입력 가능하게 설정
        connection.setDoOutput(true); // 출력 가능하게 설정

        JSONObject data = new JSONObject();
        data.put("model", "gpt-3.5-turbo"); // gpt 모델 설정
        data.put("temperature", 0.7); // 답변 정확도 설정

        JSONObject message = new JSONObject(); // json 객체 생성
        message.put("role", "user");
        message.put("content", String.valueOf(request.getParameter("question"))); // 질문 내용 작성

        JSONArray messages = new JSONArray(); // Json 객체를 담을 배열 생성
        messages.put(message); // 

        data.put("messages", messages);

        BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(connection.getOutputStream()));
        bw.write(data.toString()); // data 객체를 문자열로 변환하여 전송
        bw.flush();
        bw.close();

        BufferedReader br = new BufferedReader(new InputStreamReader(connection.getInputStream()));
        StringBuilder sb = new StringBuilder();
        String line;

        while ((line = br.readLine()) != null) {
            sb.append(line);
        }

        br.close();

        // JSON 파싱
        JSONObject jsonResponse = new JSONObject(sb.toString());
        JSONArray choices = jsonResponse.getJSONArray("choices");
        JSONObject firstChoice = choices.getJSONObject(0);
        JSONObject messageResponse = firstChoice.getJSONObject("message");
        String content = messageResponse.getString("content");

        // content 출력
        out.println(content);
    }
}
```
