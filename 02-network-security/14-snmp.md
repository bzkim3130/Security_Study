# SNMP (Simple Network Management Protocol)

## 한줄 요약
> SNMP는 UDP 기반으로 네트워크 장비를 원격 관리하는 프로토콜이며, Manager가 161번 포트로 질의하고 Agent는 문제 발생 시 162번 포트로 Trap을 보낸다.

## 1. 핵심 구성요소

| 구성요소 | 역할 |
|---|---|
| Manager | 중앙에서 장비들을 모니터링/관리하는 주체 |
| Agent | 각 네트워크 장비에서 동작, 상태정보 제공 |
| MIB (Management Information Base) | 장비의 관리정보를 트리 구조로 정의한 데이터베이스 |
| OID (Object Identifier) | MIB 내 특정 항목을 가리키는 고유 번호 |

## 2. 포트 번호

| 포트 | 용도 |
|---|---|
| UDP 161 | Manager → Agent 질의 (GetRequest 등) |
| UDP 162 | Agent → Manager 이상 알림 (Trap) |

## 3. 통신 흐름

```
[Manager]                              [Agent]
   │── GetRequest (UDP 161) ─────────▶│
   │◀──── GetResponse ──────────────│
   │                                     │
   │◀──── Trap (UDP 162, 이상 발생시) ──│  (Manager 요청 없이 Agent가 자발적 전송)
```

## 4. PDU(프로토콜 데이터 단위) 종류

| PDU | 용도 |
|---|---|
| GetRequest | 특정 값 조회 요청 |
| GetNextRequest | 다음 항목 조회 (트리 순회) |
| SetRequest | 값 설정/변경 요청 |
| GetResponse | 요청에 대한 응답 |
| Trap | Agent가 자발적으로 보내는 이상 알림 |

## 5. 버전 비교

| 버전 | 보안 방식 | 특징 |
|---|---|---|
| SNMPv1 | Community String (평문) | 최초 버전, 보안 취약 |
| SNMPv2c | Community String (평문) | 성능 개선, 보안은 v1과 동일하게 취약 |
| SNMPv3 | USM (사용자 기반 보안모델) | 인증+암호화 지원, 현재 권장 버전 |

## 암기 팁
- **포트**: "161=질의(GET), 162=경보(TRAP)" — 1살 어릴 때 물어보고, 2살 더 먹으면 알아서 알림
- **버전**: "v1/v2c는 평문 커뮤니티스트링(허술), v3부터 진짜 보안(USM)"
- **"SET은 값을 바꾸는 것 → 보안 취약하면 위험"** — SNMPv1/v2c 환경에서 SetRequest 노출 시 장비 설정 조작 가능

## 헷갈리는 포인트
- Community String은 "비밀번호"처럼 보이지만 **평문으로 전송**되어 스니핑에 매우 취약 (v1/v2c의 근본적 약점)
- Trap은 Manager의 요청 없이 **Agent가 자발적으로** 보내는 유일한 PDU — 나머지는 다 Manager가 먼저 요청

## 관련 기출/문제
- 161/162 포트 용도 구분
- PDU 종류별 기능 매칭
- SNMPv1/v2c/v3 보안 수준 비교

## 💬 내 코멘트
- 
