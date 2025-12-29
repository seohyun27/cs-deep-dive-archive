# 🧠 Page Replacement Policy Simulator (GUI)
> **다양한 페이지 교체 알고리즘(FIFO, LFU, MFU)과 직접 제안한 PFU 정책의 성능 비교 시뮬레이터**

## 📖 Overview
운영체제의 메모리 관리 기법인 <b>페이지 교체 정책(Page Replacement Policy)</b>들이 실제로 어떻게 동작하는지 시각적으로 확인하기 위해 개발한 Java Swing 애플리케이션입니다.<br/>
기존의 정형화된 알고리즘 외, 확률적 접근을 사용하는 <b>페이지 교체 정책(Page Replacement Policy)</b> 정책을 직접 구현하여 성능 차이를 비교했습니다.

* **Language:** Java 17
* **Framework:** Java Swing (GUI)
* **Policies:** FIFO, LFU, MFU, **PFU (Custom)**

## 📷 Preview
*동일한 조건(문자열: ABBBCDEFB, 프레임: 4)에서 정책별 실행 결과 비교입니다.*

<table border="0">
  <tr>
    <td align="center" width="50%">
      <img width="100%" src="https://github.com/user-attachments/assets/63a87d11-2568-47a4-b899-22115237c67f" alt="FIFO 결과"/>
      <br>
      <b>① FIFO Policy</b>
    </td>
    <td align="center" width="50%">
      <img width="100%" src="https://github.com/user-attachments/assets/ceb06bce-6c92-49ea-b8f8-4573054243d4" alt="LFU 결과"/>
      <br>
      <b>② LFU Policy</b>
    </td>
  </tr>
  <tr>
    <td align="center" width="50%">
      <img width="100%" src="https://github.com/user-attachments/assets/eba70205-ce12-43db-8705-4dd7d49dc5a9" alt="MFU 결과"/>
      <br>
      <b>③ MFU Policy</b>
    </td>
    <td align="center" width="50%">
      <img width="100%" src="https://github.com/user-attachments/assets/9d56d5c8-e5a2-4afa-bfa4-0a7e742dece0" alt="PFU 결과"/>
      <br>
      <b>④ PFU Policy (Custom)</b>
    </td>
  </tr>
</table>

## 📂 Project Structure
객체 지향 원리(OOP)에 따라 역할을 분리하여 구현했습니다.

* **`src/Page.java`**: 페이지의 속성(ID, 참조 횟수, 적재 시간 등)을 정의한 데이터 클래스
* **`src/Core.java`**: 교체 알고리즘의 핵심 로직(FIFO, LFU, MFU, PFU) 및 Page Fault/Hit 통계 계산 담당
* **`src/GUI.java`**: 사용자 인터페이스 구현 및 시뮬레이션 실행 (Main Entry Point)

## 💡 About PFU (Probabilistic Frequently Used)
**모든 상황에 완벽한 알고리즘은 없다**는 가설 하에 제가 직접 설계한 실험적 정책입니다.
* **개념:** 참조 횟수나 시간 등 특정규칙에만 얽매이지 않고 **확률적(Random) 요소**를 도입하여 교체 대상을 선정합니다.
* **목적:** 지역성(Locality) 패턴이 불규칙하거나 예측하기 힘든 워크로드에서 기존 알고리즘 대비 어떤 효율을 보이는지 검증하기 위함입니다.

## 🚀 How to Run
별도의 설치 없이 Java가 설치된 환경에서 바로 실행 가능합니다.

1. `src` 폴더 내의 **`GUI.java`** 파일을 컴파일 및 실행합니다.
2. GUI 창이 뜨면 시뮬레이션 할 <b>참조 문자열(Reference String)</b>과 **프레임 수**를 설정합니다.
3. 원하는 정책(FIFO, LFU, MFU, PFU)을 선택하고 **[실행]** 버튼을 누릅니다.
4. GUI의 우측 창에서 **Page Hit, Migration, Fault 횟수**와 **Fault Rate** 결과를 확인합니다.
