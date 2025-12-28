# 💻 Computer Science Deep Dive Archive
> **Theory to Practice: 백엔드 개발의 근간이 되는 CS 심화 탐구 및 구현 기록**<br/>
><br/>
> 학부 과정에서 수행한 **운영체제, 컴퓨터 구조, 네트워크** 관련 프로젝트와 기술 보고서를 아카이빙한 저장소입니다.<br/>
> 단순한 이론 학습을 넘어, 시스템의 동작 원리를 분석하고 성능 최적화 관점에서의 트레이드오프(Trade-off)를 고민했습니다.

<br>

## 📂 1. Operating System (운영체제)
CPU 스케줄링과 메모리 관리 전략을 분석하며 **시스템 리소스 효율화**에 대한 이해를 넓혔습니다.

### 1️⃣ CPU Scheduler Analysis & Design
* **주요 내용:** 다양한 스케줄링 알고리즘(FCFS, SJF, RR 등)을 직접 설계하고 시뮬레이션하여 성능을 비교 분석함.
* **Key Insight:**
    * "절대적인 만능 알고리즘은 없다." 시스템의 목적(응답 속도 vs 처리량)에 따라 적합한 스케줄러를 선택하거나 혼합(Hybrid)해야 함을 입증.
    * **Backend View:** 대용량 트래픽 처리 시 **스레드 풀(Thread Pool)의 스케줄링 정책**이나 OS 튜닝이 성능에 미치는 영향을 이해하는 기반이 됨.
* **[📄 보고서 PDF 보기](./Operating-System/01_CPU_Scheduling_Analysis.pdf)**

### 2️⃣ PFU(Probabilistic Frequently Used) 페이지 교체 정책 제안 및 구현
* **주요 내용:** 기존 LFU/MFU 정책이 프로그램의 **지역성(Locality)** 변화에 취약하다는 점을 분석하고, 이를 보완하는 **확률 기반의 새로운 교체 정책(PFU)**을 직접 설계 및 구현함.
* **Implementation:**
    * 기존 페이지 교체 알고리즘(FIFO, LFU, MFU)과 제안한 PFU 알고리즘을 시뮬레이션 코드로 구현.
    * 다양한 워크로드 데이터를 입력하여 `Page Fault` 발생 횟수를 측정하고 성능 우위를 입증함.
* **Key Insight:**
    * **Locality-Awareness:** 지역성 특성이 강한 환경과 그렇지 않은 환경 모두에서 안정적인 성능을 내는 알고리즘의 중요성 확인.
    * **Backend View:** DB의 **Buffer Pool 관리**나 Redis와 같은 **캐시(Cache) 시스템의 만료 정책(Eviction Policy)**을 설계할 때, 데이터 접근 패턴(Access Pattern)을 고려해야 함을 체득.
* **[📂 소스 코드 및 보고서 보기](./Operating-System/02_Page_Replacement_PFU_Proposal/)**

* <br>

## 📂 2. Computer Architecture (컴퓨터 구조)
하드웨어의 구조적 특징과 병목 현상을 이해하여 **소프트웨어 최적화**의 하드웨어적 근거를 마련했습니다.

### 1️⃣ Processor System Comparison (MIPS vs ARM vs Intel vs NVIDIA)
* **주요 내용:** 임베디드(ARM), PC(Intel Skylake), 슈퍼컴퓨터(NVIDIA A100) 프로세서의 아키텍처를 분석하고, 목적에 따른 **ISA 및 파이프라인 설계의 트레이드오프**를 규명.
* **Key Insight:**
    * **ARM:** Throughput을 포기하고 전력/비용 효율을 선택 (3단계 파이프라인)
    * **Intel:** 전력 소모를 감수하고 단일 스레드 성능 극대화 (복잡한 비순차 실행)
    * **NVIDIA:** 단일 속도를 포기하고 대규모 병렬 처리 선택 (SIMT, Latency Hiding)
    * **Backend View:** 서버 환경 구축 시, 워크로드의 특성(단일 연산 vs 병렬 처리)에 따라 **적절한 인스턴스 타입(CPU Optimized vs GPU)**을 선택해야 하는 이유를 하드웨어 레벨에서 이해함.
* **[📄 보고서 PDF 보기](./Computer-Architecture/01_Processor_System_Comparison.pdf)**

### 2️⃣ High-Associativity 캐시 교체 정책 성능 분석 및 구현
* **주요 내용:** `SimpleScalar` 시뮬레이터를 수정하여 **MRU(Most Recently Used)** 정책을 직접 구현하고, 캐시 연관도(Associativity) 변화에 따른 LRU, Random, MRU의 성능 차이를 `gcc`, `mcf` 벤치마크로 측정.
* **Key Insight:**
    * 제한된 용량에서는 복잡한 LRU보다 **비용이 낮은 Random 정책**이 가성비(Trade-off) 면에서 합리적일 수 있음을 데이터로 입증.
    * **Backend View:** 무조건 복잡하고 정교한 알고리즘이 좋은 것이 아니라, **구현 복잡도 대비 성능 효율(Cost-Benefit Analysis)**을 따져 기술을 선택해야 함을 배움.
* **[📂 프로젝트 코드 및 보고서 보기](./Computer-Architecture/02_Cache_Replacement_Policy/)**

<br>

## 📂 3. Network Programming (네트워크)
소켓 프로그래밍을 통해 백엔드의 핵심인 **동시성 처리(Concurrency)**와 **I/O**를 깊이 있게 탐구했습니다.

### 1️⃣ Multi-threading 기반 1:1 귓속말 채팅 서버 구현
* **주요 내용:** 기존의 단순 브로드캐스트 채팅 서버(`chat_serv.c`)를 개선하여, 멀티 스레드 환경에서 **1:1 귓속말 기능**과 **동시 읽기/쓰기**가 가능하도록 고도화.
* **Tech Stack:** `C`, `Socket Programming`, `POSIX Threads (pthread)`, `Mutex`
* **Implementation Details:**
    * **Protocol Design:** `@receiver message` 포맷을 파싱하여 특정 클라이언트에게만 메시지를 라우팅하는 로직 구현 (`send_whisper_msg`).
    * **Concurrency Control:** `clnt_names`(사용자 목록) 등 전역 변수에 여러 스레드가 동시에 접근할 때 발생하는 Race Condition을 방지하기 위해 **Mutex를 활용한 임계 영역(Critical Section) 보호** 적용.
    * **Non-blocking I/O:** 클라이언트에서 읽기 스레드와 쓰기 스레드를 분리하여, 메시지를 입력하는 도중에도 수신된 메시지를 즉시 출력할 수 있도록 개선.
* **Key Insight:**
    * **Backend View:** 다수의 클라이언트 요청을 동시에 처리하는 백엔드 서버의 기본 원리를 체득하고, **스레드 안전(Thread-safe)**한 코드 작성의 중요성을 경험함.
* **[📂 소스 코드 및 보고서 보기](./Network-Programming/Multi-Thread-Chat/)**