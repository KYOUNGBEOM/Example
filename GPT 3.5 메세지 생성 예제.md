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

// MyServlet 클래스는 HttpServlet을 상속받아 웹 요청을 처리하는 서블릿 클래스
public class MyServlet extends HttpServlet {
    private static String API_KEY = "API 키"; // API 키를 저장하는 상수
    private static String GPT_URL = "https://api.openai.com/v1/chat/completions"; // GPT URL을 저장하는 상수

    // MyServlet 생성자, HttpServlet의 기본 생성자를 호출
    public MyServlet() {
        super();
    }

    // doGet 메서드는 GET 요청을 처리
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        response.setContentType("text/html; charset=UTF-8"); // 응답의 콘텐츠 타입을 HTML로 설정하고, UTF-8 인코딩을 사용
        PrintWriter out = response.getWriter(); // PrintWriter 객체를 통해 서블릿이 클라이언트(웹브라우저)로 데이터 전송
        out.println("doGet 호출됨 <br>"); // 클라이언트로 문자열을 출력

        gpt(request, out); // gpt 메서드를 호출하여 OpenAI GPT-3.5 API와 통신
    }

    // doPost 메서드는 POST 요청을 처리
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        response.setContentType("text/html; charset=UTF-8"); // 응답의 콘텐츠 타입을 HTML로 설정하고, UTF-8 인코딩을 사용
        PrintWriter out = response.getWriter(); // response로부터 PrintWriter 객체를 가져와 서블릿이 클라이언트(웹브라우저)로 데이터 전송
        out.println("doPost 호출됨"); // 클라이언트로 문자열을 출력
    }

    // gpt 메서드는 OpenAI GPT-3.5 API를 호출하여 질문에 대한 응답을 받음
    private void gpt(HttpServletRequest request, PrintWriter out) throws IOException {
        URL url = new URL(GPT_URL); // URL 객체를 생성
        HttpURLConnection connection = (HttpURLConnection) url.openConnection(); // URL 객체를 사용하여 HttpURLConnection 객체를 생성

        connection.setRequestMethod("POST"); // 요청 메서드로 POST를 설정
        connection.setRequestProperty("Content-Type", "application/json"); // 요청 헤더에 Content-Type을 JSON으로 설정
        connection.setRequestProperty("Authorization", "Bearer " + API_KEY); // 요청 헤더에 인증 토큰을 설정
        connection.setDoInput(true); // 입력을 허용
        connection.setDoOutput(true); // 출력을 허용

        JSONObject data = new JSONObject(); // 요청 데이터를 담을 JSON 객체를 생성
        data.put("model", "gpt-3.5-turbo"); // GPT 모델을 설정
        data.put("temperature", 0.7); // 답변의 창의성(온도)을 설정

        JSONObject message = new JSONObject(); // 메시지 내용을 담을 JSON 객체를 생성
        message.put("role", "user"); // 메시지의 역할을 사용자로 설정
        message.put("content", String.valueOf(request.getParameter("question"))); // 요청에서 질문을 가져와 메시지 내용으로 설정

        JSONArray messages = new JSONArray(); // 메시지 객체를 담을 JSON 배열을 생성
        messages.put(message); // 메시지 객체를 배열에 추가

        data.put("messages", messages); // 데이터를 담는 JSON 객체에 메시지 배열을 추가

        BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(connection.getOutputStream())); // 출력 스트림을 설정
        bw.write(data.toString()); // JSON 객체를 문자열로 변환하여 출력 스트림에 작성
        bw.flush(); // 스트림을 비움
        bw.close(); // 스트림을 닫음

        BufferedReader br = new BufferedReader(new InputStreamReader(connection.getInputStream())); // 입력 스트림을 설정
        StringBuilder sb = new StringBuilder(); // 응답을 담을 StringBuilder 객체를 생성
        String line;

        while ((line = br.readLine()) != null) { // 응답 데이터를 한 줄씩 읽어들임
            sb.append(line); // 읽어들인 데이터를 StringBuilder에 추가
        }

        br.close(); // 입력 스트림을 닫음음

        // JSON 응답 파싱
        JSONObject jsonResponse = new JSONObject(sb.toString()); // 응답 문자열을 JSON 객체로 변환
        JSONArray choices = jsonResponse.getJSONArray("choices"); // 응답에서 choices 배열을 가져옴
        JSONObject firstChoice = choices.getJSONObject(0); // 첫 번째 선택지를 가져옴
        JSONObject messageResponse = firstChoice.getJSONObject("message"); // 선택지에서 메시지 객체를 가져옴
        String content = messageResponse.getString("content"); // 메시지 내용에서 content 문자열을 가져옴

        // content 출력
        out.println(content); // 클라이언트로 content 문자열을 출력
    }
}
}
```
