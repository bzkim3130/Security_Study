# 무선보안

## 한줄 요약
> 무선 암호화는 WEP(취약) → WPA(임시 개선) → WPA2(AES 표준) → WPA3(최신, 최고 보안)로 발전했다.

## 1. 무선 암호화 프로토콜 비교

| 프로토콜 | 암호화 알고리즘 | 보안 상태 | 특징 |
|---|---|---|---|
| WEP | RC4 | 취약 (사용 중단 권고) | 고정 키, IV 짧아서 크래킹 쉬움 |
| WPA | RC4 + TKIP | WEP보다 개선, 여전히 취약점 있음 | 키를 주기적으로 자동 변경(TKIP) |
| WPA2 | AES + CCMP | 안전 (오랜 표준) | 사실상 업계 표준, KRACK 취약점 존재 |
| WPA3 | AES-256 + SAE | 최신, 가장 안전 | 오프라인 사전공격에 강함, 개인정보보호 강화 |

## 2. 핵심 키워드

- **SSID**: 무선 네트워크 이름. 숨겨도 스니핑으로 쉽게 알아낼 수 있어 보안 수단으로는 약함
- **TKIP (Temporal Key Integrity Protocol)**: WPA에서 도입, 패킷마다 키를 다르게 써서 WEP의 취약점 보완
- **CCMP**: WPA2에서 사용하는 AES 기반 암호화 방식
- **SAE (Simultaneous Authentication of Equals)**: WPA3의 새로운 인증 방식, 4-way 핸드셰이크의 오프라인 사전공격 취약점(WPA2의 문제)을 보완
- **Evil Twin**: 정상 AP와 동일한 SSID로 위장한 가짜 AP를 만들어 사용자를 유인하는 공격
- **Rogue AP**: 조직 내부에 허가 없이 설치된 무단 AP (내부 보안 구멍이 됨)

## 3. 주요 무선 공격

| 공격 | 원리 |
|---|---|
| WEP 크래킹 | 짧은 IV 재사용을 이용해 키 유추 |
| Evil Twin | 가짜 AP로 사용자 유인, 트래픽 가로채기 |
| Deauth 공격 | 인증 해제 프레임을 위조해 정상 사용자 강제 연결 끊기 |
| KRACK | WPA2의 4-way 핸드셰이크 재전송 취약점을 이용한 키 재설치 공격 |

## 암기 팁
- **버전 발전 순서**: "웹(WEP)은 약하다 → 더블유피에이(WPA)로 땜빵 → 더블유피에이투(WPA2)가 오래 쓴 표준 → 더블유피에이쓰리(WPA3)가 최신"
- **암호화 알고리즘**: "WEP·WPA는 RC4(스트림), WPA2부터 AES(블록)"
- **Evil Twin = "쌍둥이 행세"**, **Rogue AP = "몰래 설치된 불청객"**

## 4. IEEE 802.11i 키 관리

802.11i는 **4-Way Handshake**로 PMK에서 PTK(유니캐스트용)와 GTK(브로드캐스트용)를 안전하게 유도·분배하는 키 계층 구조를 정의.

### 키 계층 구조

| 키 | 생성 방식 | 용도 | 수명 |
|---|---|---|---|
| PMK (Pairwise Master Key) | PSK방식: 패스워드→PBKDF2 / 802.1X방식: EAP인증 후 AS가 생성해 AP로 전달 | 세션 최상위 마스터키 | 인증 세션 동안 유지 |
| PTK (Pairwise Transient Key) | PMK+ANonce+SNonce+AP MAC+STA MAC → PRF | 유니캐스트 트래픽 암호화 | 4-Way Handshake마다 갱신 |
| GTK (Group Temporal Key) | AP가 임의 생성, PTK로 암호화해 STA에 전달 | 브로드캐스트/멀티캐스트 암호화 | 그룹키 갱신 주기마다 갱신 |
| GMK (Group Master Key) | AP 내부 생성 | GTK 유도의 상위키 | AP 내부 관리 |

**PTK의 하위 구성 3개**
- KCK (Key Confirmation Key): 4-Way Handshake 메시지 무결성(MIC) 검증
- KEK (Key Encryption Key): GTK 등 키 데이터 암호화 전달
- TK (Temporal Key): 실제 데이터 프레임 암호화(CCMP/TKIP)

### 4-Way Handshake 흐름

```
STA(단말)                              AP
   |<-- 1. ANonce ----------------------|
   (STA: SNonce 생성 후 PTK 계산)
   |-- 2. SNonce + MIC ----------------->|
                                    (AP: PTK 계산 완료)
   |<-- 3. GTK(KEK로 암호화) + MIC -------|
   (STA: GTK 설치)
   |-- 4. ACK(설치확인) + MIC ----------->|
   (이후 TK로 데이터 암호화 시작)
```

## 암기 팁
- **"PMK는 씨앗, PTK는 매번 새로 자라는 나무"** — PMK는 안 바뀌고 PTK는 매 핸드셰이크마다 Nonce가 달라 매번 새로 유도(재생공격 방지)
- **PTK 3형제 "KCK-KEK-TK" = "확인-암호화-전송"**
- **"1,2,3,4 = Nonce,Nonce,GTK,ACK"** 순서로 암기
- **버전 발전 순서**: "웹(WEP)은 약하다 → 더블유피에이(WPA)로 땜빵 → 더블유피에이투(WPA2)가 오래 쓴 표준 → 더블유피에이쓰리(WPA3)가 최신"
- **암호화 알고리즘**: "WEP·WPA는 RC4(스트림), WPA2부터 AES(블록)"
- **Evil Twin = "쌍둥이 행세"**, **Rogue AP = "몰래 설치된 불청객"**

## 헷갈리는 포인트
- WPA와 WPA2는 암호화 알고리즘 자체가 다름 (WPA=RC4/TKIP, WPA2=AES/CCMP) — 이름만 비슷하고 완전히 다른 세대
- KRACK은 WPA2 프로토콜 자체의 설계 취약점이지, 암호 알고리즘(AES) 자체가 뚫린 게 아님 (4-Way Handshake 3번 메시지 재전송 유도로 Nonce 재사용 강제 → TK 재설치로 키스트림 재사용)
- **PMK ≠ 실제 암호화 키**: PMK는 절대 전송되지 않고 직접 데이터 암호화에도 안 쓰임(실사용은 TK)
- **GTK는 PTK 파생물이 아님**: 별도 생성되어 PTK(KEK)로 "포장"되어 전달되는 것

## 관련 기출/문제
- WEP/WPA/WPA2/WPA3 암호화 방식 매칭
- Evil Twin vs Rogue AP 구분
- WPA2 KRACK 취약점의 원리
- PMK/PTK/GTK 키 계층 구분, 4-Way Handshake 순서
- PTK 하위 3개 키(KCK/KEK/TK)의 역할

## 💬 내 코멘트
- 
