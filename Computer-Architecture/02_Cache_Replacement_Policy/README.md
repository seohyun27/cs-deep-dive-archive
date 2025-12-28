# 🧠 SimpleScalar Cache Replacement Policy (MRU Implementation)
> **SimpleScalar 3.0 시뮬레이터를 개조하여 캐시 교체 정책(MRU)을 직접 구현하고 성능을 분석한 프로젝트**

## 📖 Overview
본 프로젝트는 컴퓨터 구조 학습 목적으로 **SimpleScalar 3.0** 시뮬레이터의 소스 코드를 분석하고 수정하여, 기존에 지원하지 않던 **MRU(Most Recently Used)** 페이지 교체 정책을 추가 구현했습니다.<br/>
또한, **High-Associativity** 환경에서 `LRU`, `Random`, `MRU` 정책 간의 성능 트레이드오프(Trade-off)를 `gcc`, `mcf` 벤치마크를 통해 정량적으로 분석했습니다.

* **Simulator:** SimpleScalar 3.0 Toolset
* **Language:** C (Legacy Code)
* **Environment:** Linux (Ubuntu 22.04 on VirtualBox) w/ GCC 3.4

## 📂 Repository Contents
SimpleScalar의 전체 소스 코드는 저작권 및 용량 문제로 포함하지 않았으며, 제가 직접 **로직을 수정하고 구현한 핵심 파일**만 업로드하였습니다.

* **`src/cache.c`**: 캐시 접근(`cache_access`) 및 교체 정책(Switch-Case) 로직 구현 (**핵심**)
* **`src/cache.h`**: 캐시 블록/세트 구조체 및 정책 Enum 정의
* **`Report.pdf`**: 실험 설계 및 결과 분석 전체 보고서

## 🛠️ Implementation Details
### 1. 트러블 슈팅: 레거시 환경 구축
SimpleScalar 3.0은 구버전 C 문법으로 작성되어 최신 GCC에서 컴파일 에러가 발생했습니다.
* **Solution:** `sudo apt-get install gcc-3.4`를 통해 구버전 컴파일러 환경을 구축하고, `make config-alpha`를 통해 Alpha 아키텍처 타겟으로 빌드를 성공시켰습니다.

### 2. MRU (Most Recently Used) 알고리즘 구현
기존 `cache.c`의 `cache_access` 함수 내 `switch` 문에 **MRU 로직**을 추가했습니다.
* **LRU (기존):** 리스트의 Tail(가장 오래된 블록)을 희생양으로 선택 후 Head로 이동.
* **MRU (구현):** 시간적 지역성을 역행하기 위해 **Head(가장 최근 블록)를 즉시 희생양으로 선택.** 별도의 리스트 갱신(Update) 과정이 필요 없어 연산 비용이 절감됨.

```c
// cache.c 수정 내용 요약
switch (cp->policy) {
    case LRU:
    case FIFO:
        repl = cp->sets[set].way_tail; // 꼬리(오래된 것) 선택
        update_way_list(&cp->sets[set], repl, Head);
        break;
        
    case MRU: // [New] 직접 구현한 부분
        repl = cp->sets[set].way_head; // 머리(최근 것) 바로 선택
        // MRU는 이미 Head에 있으므로 위치 이동 불필요 (최적화)
        break;
}
```

## 🧪 Experiment Setup

* **Benchmarks:**
  * `mcf`: 지역성이 낮은 Cache Killer 워크로드 (포인터 연산 다수)
  * `gcc`: 일반적인 정수 연산 워크로드
* **Parameters:** L1 D-Cache (16KB), Block Size (32B)
* **Variables:** Associativity (2-way ~ 32-way)

## 🚀 How to Run & Simulation

### 1. Execution Command

`sim-cache`를 사용하여 벤치마크 프로그램을 시뮬레이션합니다. 아래 명령어는 512 Sets, 32B Block, 1-way, LRU 정책을 적용한 예시입니다.

```bash
./sim-cache -redir:sim cache.txt -max:inst 1000000000 \
  -cache:dl1 dl1:512:32:1:l \
  ../benchmark/gzip/gzip00.peak.ev6 \
  ../benchmark/gzip/input.combined

```

### 2. Simulation Screenshot

<img src="./simulation_run.png" alt="SimpleScalar Execution Screenshot" width="800">

*터미널에서 `sim-cache` 명령어를 실행하고, 초기화 메시지 및 시뮬레이션 결과가 생성되는 화면입니다.*

## 📊 Key Results (Summary)

* **MRU 성능:** 지역성이 높은 프로그램에서는 성능이 저하되었으나, 이를 통해 역설적으로 **캐시에서의 지역성(Locality) 유지의 중요성**을 입증했습니다.
* **Random vs LRU:** 16KB의 제한된 용량에서는 복잡한 `LRU`와 단순한 `Random` 정책 간의 미스율 차이가 0.2% 내외로 미미했습니다. 이를 통해 하드웨어 복잡도를 고려할 때 **Random 정책의 가성비**가 유효함을 확인했습니다.

