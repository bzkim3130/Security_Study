# OSI 7계층 모델

## 한줄 요약
> 데이터는 송신 시 위(응용)에서 아래(물리)로 캡슐화되고, 수신 시 아래에서 위로 역캡슐화되며, 각 계층마다 PDU 명칭과 대표 장비/프로토콜이 다르다.

## 1. 7계층 구조표

| 계층 | 이름 | 주요 기능 | PDU | 대표 장비/프로토콜 |
|---|---|---|---|---|
| 7 | 응용(Application) | 사용자 서비스 제공 | Data | HTTP, FTP, DNS, TLS |
| 6 | 표현(Presentation) | 인코딩/암호화/압축 | Data | SSL/TLS, JPEG, ASCII |
| 5 | 세션(Session) | 세션 수립/유지/종료 | Data | NetBIOS, RPC |
| 4 | 전송(Transport) | 종단간 신뢰성 있는 전달 | Segment(TCP)/Datagram(UDP) | TCP, UDP |
| 3 | 네트워크(Network) | 논리주소(IP) 기반 라우팅 | Packet | IP, 라우터 |
| 2 | 데이터링크(Data Link) | MAC주소 기반 물리적 전달, 오류검출 | Frame | 스위치, MAC/LLC |
| 1 | 물리(Physical) | 전기적/광학적 신호 전송 | Bit | 케이블, 허브, 리피터 |

## 2. 계층 구조 도식화

```
[송신측: 캡슐화]                    [수신측: 역캡슐화]
7 응용    Data                       7 응용    Data
6 표현    Data                       6 표현    Data
5 세션    Data                       5 세션    Data
4 전송    [Header]Data → Segment     4 전송    Segment → [Header]Data
3 네트워크 [Header]Segment → Packet  3 네트워크 Packet → [Header]Segment
2 링크    [Header]Packet[Trailer]    2 링크    Frame → [Header]Packet[Trailer]
         → Frame
1 물리    Frame → Bit스트림 전송      1 물리    Bit스트림 → Frame
        ↓ (전송)                            ↑ (수신)
```

## 3. 주요 용어

- **캡슐화(Encapsulation)**: 상위 계층 데이터에 자기 계층의 헤더(+트레일러)를 붙여 하위로 전달
- **역캡슐화(Decapsulation)**: 수신측에서 각 계층 헤더를 순서대로 제거하며 상위로 전달
- **논리주소(IP, L3) vs 물리주소(MAC, L2)**: IP는 네트워크 간 라우팅용(변경 가능), MAC은 로컬 네트워크 내 실제 전달용(장비 고유)

## 암기 팁
- **"응표세전 네링물"** (7~1층 순서 앞글자) — 응용-표현-세션-전송-네트워크-데이터링크-물리
- 영어 니모닉: **"All People Seem To Need Data Processing"** (7층부터 1층)
- **PDU 암기**: "데이터(7~5) - 세그먼트(4,TCP) - 패킷(3) - 프레임(2) - 비트(1)"

## 헷갈리는 포인트
- UDP는 "세그먼트"라 안 부르고 **데이터그램(Datagram)**이라고도 함 — 엄밀히는 TCP=Segment, UDP=Datagram으로 구분하는 교재도 있음
- 표현 계층(암호화/인코딩)과 세션 계층(연결관리)의 역할을 헷갈리기 쉬움 — TLS는 표현계층 기능(암호화)이지만 실제로는 여러 계층에 걸쳐 동작한다고 보는 시각도 있음
- 라우터는 3계층 장비, 스위치는 2계층 장비, 허브/리피터는 1계층 장비 — 장비별 계층 매칭이 자주 출제됨

## 관련 기출/문제
- 7계층 순서 및 이름 나열
- 계층별 PDU 명칭 매칭
- 계층별 대표 장비(허브/스위치/라우터) 구분
- 캡슐화/역캡슐화 개념 문제

## 💬 내 코멘트
- 
