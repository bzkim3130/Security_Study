# 스위칭 환경에서의 스니핑 기법

## 한줄 요약
> 스위치는 원래 필요한 포트로만 트래픽을 보내 스니핑이 어렵지만, MAC 플러딩/ARP 스푸핑 등으로 이 정상 동작을 깨뜨려 도청이 가능해진다.

## 1. 주요 기법 비교표

| 기법 | 원리 | 대상 |
|---|---|---|
| MAC 플러딩 | 위조 MAC 대량 전송으로 CAM 테이블 오버플로우 → 스위치가 허브처럼 브로드캐스트 | 스위치 자체(L2) |
| ARP 스푸핑/포이즈닝 | 가짜 ARP 응답으로 MAC-IP 매핑 조작 | 특정 호스트 간 통신(L2/L3) |
| ARP Redirect | 공격자가 자신을 라우터로 위장해 ARP 캐시 조작 | 게이트웨이 트래픽 |
| ICMP Redirect | 위조 ICMP Redirect 메시지로 라우팅 경로 조작 | L3 라우팅 |
| Switch Port Mirroring 악용 | SPAN 포트 설정 취약점을 악용해 트래픽 복사·탈취 | 관리자 설정 오남용 |
| DHCP 스푸핑 | 가짜 DHCP 서버로 잘못된 게이트웨이/DNS 정보 배포 | 신규 접속 클라이언트 |
| STP 조작(BPDU 위조) | 가짜 BPDU로 자신을 Root Bridge로 위장 → 트래픽 경로 조작 | 스위치 토폴로지(L2) |

## 1-1. STP(Spanning Tree Protocol) 조작 공격

**정상 STP 동작**: 스위치들이 BPDU(Bridge Protocol Data Unit)를 교환해 루프 없는 트리 구조를 만들고, 가장 우선순위 높은 스위치가 **Root Bridge**로 선출됨

```
[정상 상태]
Switch A(Root Bridge) ─── Switch B ─── Switch C
     (정상 트래픽 흐름)

[공격]
공격자 → 낮은 Bridge ID를 가진 위조 BPDU 대량 전송
     → 공격자 장비가 새로운 Root Bridge로 선출됨
     → 모든 스위치 간 트래픽이 공격자를 거쳐가도록 경로 변경(MITM)
```

**대응**: BPDU Guard(비신뢰 포트에서 BPDU 수신 시 포트 차단), Root Guard(특정 포트가 Root Bridge가 되는 것 방지)

## 2. MAC 플러딩 흐름

```
공격자 → (위조 MAC 대량 전송) → 스위치
                                    │
                          CAM 테이블(용량 제한) 가득 참
                                    │
                        스위치가 목적지를 못 찾음
                                    │
                     Fail-Open → 모든 포트로 브로드캐스트(허브처럼 동작)
                                    │
                          공격자가 모든 트래픽 도청 가능
```
→ 이 상태를 "**Switch Jamming**"이라고도 부름

## 3. ARP 스푸핑 흐름 (MITM)

```
정상: PC-A ←ARP→ PC-B (서로의 MAC 주소를 정확히 앎)

공격: 공격자가 PC-A에게 "내가 PC-B의 MAC이다"라고 위조 응답
      공격자가 PC-B에게 "내가 PC-A의 MAC이다"라고 위조 응답
      → PC-A ↔ 공격자 ↔ PC-B 로 모든 트래픽이 공격자 경유(MITM)
```

## 4. 계층별 분류

- **L2 공격**: MAC 플러딩, ARP 스푸핑, STP 조작(BPDU 위조)
- **L3 공격**: ICMP Redirect, DHCP 스푸핑(응용/네트워크 혼합)

## 5. 대응 방법 (공격-대응 매칭)

| 공격 | 대응 |
|---|---|
| MAC 플러딩 | Port Security (포트당 허용 MAC 수 제한) |
| ARP 스푸핑 | DAI (Dynamic ARP Inspection) |
| STP 조작 | BPDU Guard, Root Guard |
| DHCP 스푸핑 | DHCP Snooping (신뢰된 포트만 DHCP 응답 허용) |
| ICMP Redirect | 라우터에서 ICMP Redirect 수신 차단 설정 |

## 암기 팁
- **"MAC플러딩 = 스위치 배 터뜨려서 허브로 만들기"**
- **"ARP스푸핑 = 문패 바꿔치기"**
- **대응 3종 세트**: "Port Security(MAC플러딩) - DAI(ARP스푸핑) - DHCP Snooping(DHCP스푸핑)"
- **"맥아스"**: MAC플러딩 → ARP스푸핑 → STP조작 순서로 스위칭 공격 3대장 암기
- **"STP조작 = 가짜 대장 행세(Root Bridge 위장)"**

## 헷갈리는 포인트
- MAC 플러딩은 **스위치 자체를 무력화**(허브화)시키는 공격, ARP 스푸핑은 **특정 호스트 간 매핑 정보를 속이는** 공격 — 대상이 다름
- Switch Jamming은 MAC 플러딩의 동의어일 뿐, 별개 공격이 아님
- **STP 조작은 "탐지 도구"가 아니라 "공격 기법"** — BPDU Guard/Root Guard는 이를 막는 대응책이지 공격이 아님(혼동 주의)

## 관련 기출/문제
- MAC 플러딩과 ARP 스푸핑의 차이
- 각 공격에 대한 대응 기법 매칭 (Port Security/DAI/DHCP Snooping/BPDU Guard)
- CAM 테이블 오버플로우 원리
- STP 조작으로 Root Bridge를 위장하는 원리

## 💬 내 코멘트
- 
