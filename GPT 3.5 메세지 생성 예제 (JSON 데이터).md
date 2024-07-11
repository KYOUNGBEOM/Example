```java
import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.OutputStreamWriter;
import java.net.HttpURLConnection;
import java.net.URL;

import org.json.JSONArray;
import org.json.JSONObject;

public class ChatGPT {
    // OpenAI API를 사용하기 위한 API 키
    private static String API_KEY = "API키 입력";
    // GPT-3.5 API의 URL
    private static String GPT_URL = "https://api.openai.com/v1/chat/completions";

    public static void main(String[] args) throws IOException {
        // URL 객체 생성
        URL url = new URL(GPT_URL);
        // HttpURLConnection 객체 생성하여 URL 연결
        HttpURLConnection connection = (HttpURLConnection)url.openConnection();
        
        // HTTP POST 메서드 설정
        connection.setRequestMethod("POST");
        // 요청 헤더 설정: JSON 데이터를 전송할 것임을 명시
        connection.setRequestProperty("Content-Type", "application/json");
        // 인증 헤더 설정: API 키를 Bearer 토큰 형식으로 전송
        connection.setRequestProperty("Authorization", "Bearer " + API_KEY);
        // 입력을 가능하게 설정
        connection.setDoInput(true);
        // 출력을 가능하게 설정
        connection.setDoOutput(true);
        
        // 전송할 JSON 데이터 생성
        JSONObject data = new JSONObject();
        // 사용할 GPT 모델 선택
        data.put("model", "gpt-3.5-turbo");
        // 생성할 텍스트의 창조적 다양성을 결정하는 온도 설정
        data.put("temperature", 0.7);
        
        // 사용자가 입력한 메시지를 JSON 형식으로 생성
        JSONObject message = new JSONObject();
        message.put("role", "user"); // 사용자 역할
        message.put("content", "안녕? Chat GPT에 대해 자세하게 소개해줘"); // 사용자 질문 내용
        
        // 메시지를 담을 배열 생성
        JSONArray messages = new JSONArray();
        messages.put(message); // 메시지 배열에 사용자 메시지 추가
        
        // 데이터에 메시지 배열 추가
        data.put("messages", messages);
        
        // 출력 스트림 생성 및 데이터 전송
        BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(connection.getOutputStream()));
        bw.write(data.toString());
        bw.flush();
        bw.close();
        
        // 서버로부터의 응답 데이터 수신
        BufferedReader br = new BufferedReader(new InputStreamReader(connection.getInputStream()));
        StringBuilder sb = new StringBuilder();
        String line = null;
        
        // 한 줄씩 읽어서 StringBuilder에 저장
        while ((line = br.readLine()) != null) {
            sb.append(line);
        }
        
        // 수신한 데이터 출력
        System.out.println(sb);
    }
}

```
