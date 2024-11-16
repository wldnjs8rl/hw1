# HW-P1: 202034827 박지원

---

## **목표**  
네트워크 애플리케이션을 직접 설계하고 구현하면서 클라이언트와 서버 간 통신의 구조와 원리를 배운다.  
Java 소켓 프로그래밍을 통해 데이터를 주고받는 방식을 이해하고, 통신 프로토콜을 설계하며 네트워크 상호작용을 구현한다.  
퀴즈 게임을 만들어보면서 다중 클라이언트를 처리하는 방법과 메시지 기반 통신 설계를 공부한다.

---

## **Requirements**

1. **네트워크 애플리케이션 설계 및 구현**  
   - Java 소켓을 이용하여 클라이언트와 서버가 통신할 수 있는 네트워크 애플리케이션을 개발한다.  
   - 클라이언트는 서버에 연결하여 질문을 받고 응답을 전송하며, 서버는 이를 평가하여 피드백과 최종 점수를 반환한다.

2. **프로토콜 정의**  
   - ASCII 기반의 애플리케이션 계층 프로토콜을 설계한다.  
   - 메시지 형식은 간단하고 명확하며, 요청과 응답 간 체계적인 데이터 교환이 이루어져야 한다.

3. **다중 클라이언트 처리**  
   - 서버는 여러 클라이언트를 동시에 처리할 수 있어야 하며, 이를 위해 ThreadPool 또는 멀티스레드를 활용한다.
     
 ---
 
##  **설계 과정**

### 1. **QUESTION**  
   - 서버는 질문을 `QUESTION` 키워드와 함께 클라이언트로 전송한다.  
   - 클라이언트는 이를 화면에 출력.  
   - **예시**: `QUESTION 1. Java는 객체지향 언어입니까? (yes/no)`

### 2. **ANSWER**  
   - 클라이언트는 입력을 받아 서버로 전송하며, 다양한 형식을 허용한다.  
   - 서버는 이를 정규화하여 처리.  
   - **예시**: `ANSWER yes`

### 3. **RESULT**  
   - 서버는 응답을 평가한 후 결과를 반환한다.  
   - **예시**: `RESULT Correct`

### 4. **ERROR**  
   - 잘못된 요청 형식에 대해 `ERROR` 메시지를 반환.  
   - **예시**: `ERROR Invalid Request`

### 5. **퀴즈 종료 및 점수 반환**  
   - 모든 질문 완료 후 최종 점수를 계산하여 클라이언트로 전송.  
   - **예시**: `퀴즈 종료! 당신의 점수는 2/3 입니다.`

---

## **구조도**


![image](https://github.com/user-attachments/assets/b15e7e3e-9980-4851-8e34-5c86dbe80b03), ![image](https://github.com/user-attachments/assets/5402272c-9467-41f0-a8bc-3337db322cb9)




- Client → Server: Request ANSWER 전송
- Server → Client: Send QUESTION, RESULT, 및 FINAL 메시지 전송
- Server → ClientHandler: 클라이언트 요청 위임
- ClientHandler → Server: 요청 처리 후 결과 반환
- Server → ConfigFile: 서버 설정 정보 로드


## **Protocol**

### **1. QUESTION**  
- **형식**: `QUESTION <질문 내용>`  
- 서버는 클라이언트에게 퀴즈 질문을 전달한다.  
- **예시**: `QUESTION 1. Java는 객체지향 언어입니까? (yes/no)`

### **2. ANSWER**  
- **형식**: `ANSWER <사용자 입력>`  
- 클라이언트는 질문에 대한 답변을 서버로 전송한다.  
- 다양한 입력을 허용하며, 서버는 이를 정규화하여 처리한다.  
  - **허용 입력 예시**: `ANSWER yes`, `ANSWER true`, `ANSWER o`

### **3. RESULT**  
- **형식**: `RESULT <Correct/Incorrect>`  
- 서버는 클라이언트의 답변에 대한 결과를 반환한다.  
- **예시**: `RESULT Correct`

### **4. ERROR**  
- **형식**: `ERROR <에러 메시지>`  
- 클라이언트가 잘못된 형식의 요청을 보낸 경우 서버는 에러 메시지를 반환한다.  
- **예시**: `ERROR Invalid Request`

### **5. FINAL**  
- **형식**: `FINAL <최종 점수>`  
- 모든 질문 종료 후 서버는 클라이언트에게 최종 점수를 전달한다.  
- **예시**: `퀴즈 종료! 당신의 점수는 2/3 입니다.`

---

## **실행 과정**

### **네트워크 소켓 통신의 기본 개념**
- **소켓**: 네트워크 상에서 두 프로그램이 데이터를 주고받기 위한 인터페이스.  
- **서버 소켓**: 클라이언트의 연결 요청을 대기하고 수락.  
- **클라이언트 소켓**: 서버에 연결 요청을 보내고 데이터를 송수신.

---

### ** 실행 과정**

1. **서버 소켓 생성**
   - Server 프로그램에서 ServerSocket 객체를 생성하여 지정된 포트(예: 1234)에서 클라이언트 연결 요청을 대기.  
   - 운영체제는 서버 소켓을 위해 포트를 열고 대기 상태로 둔다.  

2. **클라이언트 소켓 생성 및 연결**
   - Client 프로그램이 실행되면, Socket 객체를 생성하여 서버의 IP 주소와 포트를 지정.  
   - 클라이언트 소켓은 서버 소켓으로 TCP 연결 요청을 보낸다.  

3. **데이터 송수신**  
   - **질문 전송 (Server → Client)**  
     - 서버는 클라이언트 소켓의 출력 스트림을 통해 질문 메시지를 전송한다.  
   - **응답 전송 (Client → Server)**  
     - 클라이언트는 사용자 입력을 받아 서버로 전송한다.  
   - **결과 전송 (Server → Client)**  
     - 서버는 응답을 평가하여 결과 메시지를 반환한다.  

4. **연결 종료**  
   - 클라이언트와 서버는 각각 소켓을 닫아 연결을 종료한다.

---

## **코드**

### **1. Client 코드**
```java

package client;

import java.io.*;
import java.net.*;

public class Client {
    public static void main(String[] args) {
        String serverAddress = "localhost"; // 기본값: 서버 주소
        int port = 1234; // 기본값 :서버 포트 번호

        // server_info.dat 파일에서 서버 정보 읽기
        try (BufferedReader configReader = new BufferedReader(new FileReader("server_info.dat"))) {
            serverAddress = configReader.readLine(); // 첫 번째 줄: 서버 주소
            port = Integer.parseInt(configReader.readLine()); // 두 번째 줄: 포트 번호
            System.out.println("[클라이언트 로그] server_info.dat에서 서버 정보를 읽었습니다.");
        } catch (FileNotFoundException e) {
            System.out.println("[클라이언트 로그] server_info.dat 파일이 없습니다. 기본값을 사용합니다.");
        } catch (IOException e) {
            System.out.println("[클라이언트 로그] server_info.dat 파일 읽기 중 오류 발생: " + e.getMessage());
        }

        // 서버 연결 시도
        try (Socket socket = new Socket(serverAddress, port);
             BufferedReader in = new BufferedReader(new InputStreamReader(socket.getInputStream()));
             PrintWriter out = new PrintWriter(socket.getOutputStream(), true);
             BufferedReader userInput = new BufferedReader(new InputStreamReader(System.in))) {

            System.out.println("[클라이언트 로그] 서버에 연결되었습니다! (" + serverAddress + ":" + port + ")");

            String serverMessage;
            while ((serverMessage = in.readLine()) != null) {
                if (serverMessage.startsWith("QUESTION ")) {
                    // 질문을 표시
                    System.out.println(serverMessage.substring(9)); // "QUESTION " 이후 내용 출력
                    System.out.print("답변을 입력하시오: ");
                    String answer = userInput.readLine();

                    // 서버에 응답 전송
                    out.println("ANSWER " + answer);
                } else if (serverMessage.startsWith("RESULT ")) {
                    // 결과 출력
                    System.out.println("결과: " + serverMessage.substring(7)); // "RESULT " 이후 내용 출력
                } else if (serverMessage.startsWith("ERROR ")) {
                    // 에러 메시지 출력
                    System.out.println("오류: " + serverMessage.substring(6)); // "ERROR " 이후 내용 출력
                } else {
                    // 기타 메시지 출력
                    System.out.println(serverMessage);
                }
            }
        } catch (IOException e) {
            System.out.println("[클라이언트 로그] 서버 연결 중 오류 발생: " + e.getMessage());
        }
    }
}
```
---


### **2. ConfigFile 코드**
```java

package client;
import java.io.*;

public class ConfigFile {

    /**
     * server_info.dat 파일이 없으면 기본값으로 생성합니다.
     * @param filename 생성할 파일의 이름
     */
    public static void createConfigFile(String filename) {
        File configFile = new File(filename);

        // 파일이 존재하지 않을 경우 기본값으로 생성
        if (!configFile.exists()) {
            try (PrintWriter writer = new PrintWriter(new FileWriter(configFile))) {
                writer.println("localhost"); // 기본 IP
                writer.println("1234");     // 기본 포트
                System.out.println("[ConfigFile] 기본 server_info.dat 파일이 생성되었습니다.");
            } catch (IOException e) {
                System.out.println("[ConfigFile] server_info.dat 파일 생성 중 오류 발생: " + e.getMessage());
            }
        } else {
            System.out.println("[ConfigFile] server_info.dat 파일이 이미 존재합니다.");
        }
    }

    /**
     * server_info.dat 파일에서 서버 IP와 포트를 읽어옵니다.
     * @param filename 읽을 파일의 이름
     * @return 서버 IP와 포트를 포함한 배열(String[2])을 반환 (0: IP, 1: 포트)
     */
    public static String[] readConfigFile(String filename) {
        String[] serverInfo = new String[2]; // [0]: IP, [1]: 포트
        serverInfo[0] = "localhost"; // 기본값
        serverInfo[1] = "1234";      // 기본값

        // 파일 읽기
        try (BufferedReader reader = new BufferedReader(new FileReader(filename))) {
            serverInfo[0] = reader.readLine(); // 첫 번째 줄: 서버 IP
            serverInfo[1] = reader.readLine(); // 두 번째 줄: 포트 번호
            System.out.println("[ConfigFile] server_info.dat 파일에서 서버 정보를 읽었습니다.");
        } catch (FileNotFoundException e) {
            System.out.println("[ConfigFile] server_info.dat 파일이 없습니다. 기본값을 사용합니다.");
        } catch (IOException e) {
            System.out.println("[ConfigFile] server_info.dat 파일 읽기 중 오류 발생: " + e.getMessage());
        }

        return serverInfo;
    }
}

```

---


### **3. ClientHandler 코드**
```java

package server;

import java.io.*;
import java.net.*;

public class ClientHandler implements Runnable {
    private final Socket clientSocket; // 클라이언트 소켓 (final 추가)

    public ClientHandler(Socket socket) {
        this.clientSocket = socket;
    }

    @Override
    public void run() {
        try (BufferedReader in = new BufferedReader(new InputStreamReader(clientSocket.getInputStream()));
             PrintWriter out = new PrintWriter(clientSocket.getOutputStream(), true)) {

            // 퀴즈 질문과 정답
            String[] questions = {
                "1. Java는 객체지향 언어입니까? (yes/no)",
                "2. TCP는 신뢰성 있는 프로토콜입니까? (yes/no)",
                "3. HTTP는 상태 비저장(stateless) 프로토콜입니까? (yes/no)"
            };
            String[] answers = {"yes", "yes", "yes"};

            int score = 0; // 초기 점수

            // 클라이언트 연결 로그
            System.out.println("[서버 로그] 클라이언트 연결: " + clientSocket.getInetAddress());

            // 클라이언트에게 퀴즈를 시작하는 걸 알려주는 메시지를 전송
            out.println("퀴즈 게임에 오신 것을 환영합니다! 질문에 답하세요.");
            System.out.println("[서버 로그] 환영 메시지 전송");

            // 질문 순서대로 전송
            for (int i = 0; i < questions.length; i++) {
                // 질문 전송
                out.println("QUESTION " + questions[i]);
                System.out.println("[서버 로그] 질문 전송: " + questions[i]);

                // 클라이언트 응답 받기
                String clientResponse = in.readLine();
                if (clientResponse != null) {
                    System.out.println("[서버 로그] 클라이언트 응답 수신: " + clientResponse);
                } else {
                    System.out.println("[서버 로그] 클라이언트로부터 응답 없음");
                }

                // 잘못된 요청 형식 확인
                if (clientResponse == null || !clientResponse.startsWith("ANSWER ")) {
                    out.println("ERROR Invalid Request"); // 잘못된 요청 형식에 대한 메시지 전송
                    System.out.println("[서버 로그] 잘못된 요청: " + clientResponse); // 서버 로그 기록
                    continue; // 다음 질문으로 이동
                }

                // 요청에서 "ANSWER " 이후의 응답 추출 및 정규화
                String answer = clientResponse.substring(7).trim().toLowerCase();

                // 조금 다른 응답도 인식하도록 응답 정규화: true/false, o/x => yes/no
                if (answer.equals("true") || answer.equals("o")) {
                    answer = "yes";
                } else if (answer.equals("false") || answer.equals("x")) {
                    answer = "no";
                }

                // 빈 응답 체크
                if (answer.isEmpty()) {
                    out.println("ERROR Invalid Request"); // 빈 응답 처리
                    System.out.println("[서버 로그] 잘못된 요청: 빈 응답");
                    continue;
                }

                // 유효하지 않은 응답 처리
                if (!answer.equals("yes") && !answer.equals("no")) {
                    out.println("ERROR Invalid Request"); // 잘못된 응답 처리
                    System.out.println("[서버 로그] 잘못된 응답: " + answer);
                    continue;
                }

                // 정답 확인
                if (answer.equalsIgnoreCase(answers[i])) {
                    out.println("RESULT Correct");
                    System.out.println("[서버 로그] 정답 확인: Correct");
                    score++;
                } else {
                    out.println("RESULT Incorrect");
                    System.out.println("[서버 로그] 정답 확인: Incorrect");
                }
            }

            // 최종 점수 전송
            out.println("퀴즈 종료! 당신의 점수는 " + score + "/" + questions.length + " 입니다.");
            System.out.println("[서버 로그] 최종 점수: " + score + "/" + questions.length);

        } catch (IOException e) {
            // 예외 처리 로그
            System.out.println("[서버 로그] 클라이언트 처리 중 예외 발생: " + e.getMessage());
        } finally {
            // 클라이언트 소켓 닫기
            try {
                clientSocket.close();
                System.out.println("[서버 로그] 클라이언트 연결 종료: " + clientSocket.getInetAddress());
            } catch (IOException e) {
                System.out.println("[서버 로그] 클라이언트 소켓 닫기 중 오류: " + e.getMessage());
            }
        }
   
}

```

---


### **4. Server 코드**
```java

package server;

import java.io.*;
import java.net.*;
import java.util.concurrent.*;

public class Server {
    public static void main(String[] args) {
        int port = 1234; // 서버 포트 번호
        ExecutorService threadPool = Executors.newFixedThreadPool(10); // 최대 10개의 클라이언트 처리

        try (ServerSocket serverSocket = new ServerSocket(port)) {
            System.out.println("[서버 로그] 서버가 시작되었습니다. 포트: " + port);

            while (true) {
                // 클라이언트 연결 수락
                Socket clientSocket = serverSocket.accept();
                System.out.println("[서버 로그] 클라이언트 연결됨: " + clientSocket.getInetAddress());

                // ThreadPool을 사용하여 클라이언트 처리
                threadPool.execute(new ClientHandler(clientSocket));
            }
        } catch (IOException e) {
            // 서버 소켓 관련 예외 처리
            System.out.println("[서버 로그] 서버 실행 중 예외 발생: " + e.getMessage());
        } finally {
            // 서버 종료 시 ThreadPool 정리
            threadPool.shutdown();
            System.out.println("[서버 로그] 서버가 종료되었습니다.");
        }
    }
}

```
---

### **출력 **
- 서버 실행 후 클라이언트와의 통신이 정상적으로 이루어진 것을 보여주는 서버로그
![image](https://github.com/user-attachments/assets/3bcf8f63-44c1-43ec-800d-f14d50af0e3c)
![image](https://github.com/user-attachments/assets/2d2ba68a-e23a-430b-aa38-93824a908ac4)
![image](https://github.com/user-attachments/assets/29654b73-60c5-4084-8d89-2d0e7b82d112)
- 결과 화면 예시
![image](https://github.com/user-attachments/assets/69ac1bc9-69fe-4e71-b062-c276c89a1d40)
- 빈칸으로 제출하거나 완전히 이상한 답을 제출한 경우 invalid request 메시지
![image](https://github.com/user-attachments/assets/6e5f3026-3570-4506-a238-47f62be83916)
- 예외 처리
![image](https://github.com/user-attachments/assets/9d6da2aa-7a82-4d63-ad6e-447ceb6ef275)
- 다른 답안 true, false, o, x 도 가능하도록
![image](https://github.com/user-attachments/assets/21361446-9ecb-4616-94e2-ec90e6be52ab)


