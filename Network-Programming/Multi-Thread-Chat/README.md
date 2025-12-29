# 💬 Multi-Thread Chat Server with Private Messaging
> **C언어와 Socket Programming을 활용한 멀티 스레드 기반의 1:1 귓속말 채팅 프로그램**

[![Google Drive](https://img.shields.io/badge/Google%20Drive-Watch%20Demo%20Video-4285F4?style=for-the-badge&logo=googledrive&logoColor=white)](https://drive.google.com/file/d/17bwa8vEyuImeIk43u6zeR8jTpghrAkyO/view?usp=drive_link)

## 📖 Overview
기본적인 브로드캐스팅(전체 채팅)만 가능했던 채팅 서버 코드를 기반으로 **특정 사용자에게만 메시지를 보내는 1:1 귓속말 기능**을 직접 구현하여 확장한 프로젝트입니다.<br/>
<b>POSIX Threads (pthread)</b>를 사용하여 다수의 클라이언트 요청을 동시에 처리하며 **Mutex**를 통해 임계 영역(Critical Section)을 안전하게 관리합니다.

* **Language:** C
* **Environment:** Linux (Ubuntu 22.04.4)
* **Key Feature:** Multi-threading, Synchronization (Mutex), Private Message Parsing

## 📷 Preview
*서버와 다수의 클라이언트(터미널)가 연결되어 채팅을 주고받는 실행 화면입니다.*

### 👥 일반 채팅 (Broadcasting)
<img width="100%" alt="일반 채팅" src="https://github.com/user-attachments/assets/14d63f64-b0e6-46d7-8bba-7e449cb8987d" />
 
### 🤫 1:1 귓속말 (Whisper)
<img width="100%" alt="귓속말" src="https://github.com/user-attachments/assets/575caa41-6d2f-4867-a398-661b56a7108c" />

### 📢 전체 메시지 (System Message)
<img width="100%" alt="전체 메시지" src="https://github.com/user-attachments/assets/4f9e85f4-eb56-4ccf-8d72-773d47762e60" />

## ⚙️ Implementation Details
### 1. 1:1 귓속말 기능 (Whisper)
* **Protocol:** `@receiver message` 형식을 파싱하여 처리
* **Logic:**
    1. 클라이언트가 입장 시 자신의 이름(Name)을 서버에 전송.
    2. 서버는 `send_whisper_msg()` 함수를 통해 수신자 이름이 존재하는지 탐색.
    3. 존재할 경우 해당 소켓 디스크립터로만 메시지 전송, 없을 경우 에러 메시지 반환.

### 2. 동시성 제어 (Concurrency Control)
* **Mutex:** `clnt_cnt` (접속자 수) 및 `clnt_socks` (소켓 배열) 등 공유 자원에 접근할 때 Race Condition을 방지하기 위해 Mutex Lock/Unlock 적용.

## 🚀 How to Run
본 프로그램은 **Linux(Ubuntu) 환경**에서 실행을 권장합니다.

### 1. Compile
스레드 라이브러리 링크를 위해 `-pthread` 옵션이 필요합니다.
```bash
# 서버 컴파일
gcc chat_serv.c -o chat_serv -pthread

# 클라이언트 컴파일
gcc chat_clnt.c -o chat_clnt -pthread
```

### 2. Execute Server
포트 번호를 지정하여 서버를 엽니다.

```bash
./chat_serv 9190
```

### 3. Execute Client
새로운 터미널을 열어 서버 IP와 포트, 사용할 닉네임을 입력합니다. (로컬 테스트 시 IP: 127.0.0.1)

```bash
./chat_clnt 127.0.0.1 9190 [MyName]
```

### 4. Usage (Command)
* 전체 채팅: 그냥 메시지 입력 후 엔터
* 귓속말: @수신자이름 할말 (예: @david 안녕?)
