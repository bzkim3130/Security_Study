# IPSec ESP 헤더 구조와 SPI 필드

## 한줄 요약
> ESP는 SPI+Sequence Number는 평문(인증만 O, 암호화 X), Payload Data부터 Padding까지는 암호화 O, 전체(SPI~Padding까지)는 인증(HMAC) 대상이라는 3중 보호범위 구조를 가진다.

## 1. ESP 패킷 구조 (필드 순서)

```
┌─────────────────────────────────┐
│ SPI (Security Parameter Index)    │ ← 평문, 인증 O
├─────────────────────────────────┤
│ Sequence Number                    │ ← 평문, 인증 O
├─────────────────────────────────┤ ─┐
│ Payload Data (실제 데이터)          │  │
├─────────────────────────────────┤  │ 암호화 O
│ Padding                            │  │ 인증 O
├─────────────────────────────────┤  │
│ Pad Length                         │  │
├─────────────────────────────────┤  │
│ Next Header                        │ ─┘
├─────────────────────────────────┤
│ Authentication Data (ICV, HMAC값)   │ ← 이 필드 자체는 인증범위 제외(결과값이라)
└─────────────────────────────────┘
```

## 2. 필드별 암호화/인증 여부

| 필드 | 암호화 | 인증(HMAC) |
|---|---|---|
| SPI | X (평문) | **O** |
| Sequence Number | X (평문) | **O** |
| Payload Data | **O** | O |
| Padding | **O** | O |
| Pad Length | **O** | O |
| Next Header | **O** | O |
| Authentication Data | - | (계산결과 자체라 대상 아님) |

**핵심 함정(15번 문제 정답)**: "다음 중 암호화되지 않는 필드는?" → **SPI** (SPI는 수신측이 어떤 SA(보안연결)를 쓸지 식별해야 하므로 반드시 평문이어야 함)

## 3. SPI(Security Parameter Index)란?

- 수신측이 **어떤 SA(Security Association, 보안연결)**를 사용해 이 패킷을 처리해야 할지 식별하는 값
- SA는 (목적지IP, 프로토콜, SPI) 조합으로 고유하게 식별됨
- SPI가 암호화되어 있으면 수신측이 애초에 어떤 키/알고리즘을 쓸지 알 수 없어 복호화 자체가 불가능 → **반드시 평문**이어야 하는 이유

## 4. ESP vs AH(Authentication Header)

| 구분 | ESP | AH |
|---|---|---|
| 기밀성(암호화) | **제공** | 미제공 |
| 인증/무결성 | 제공 | 제공 |
| 헤더 앞부분(SPI/SeqNum) 인증 | O | O |
| 사용 빈도 | 훨씬 널리 사용(기밀성 필요하므로) | 상대적으로 적게 사용 |

## 암기 팁
- **"SPI는 문패, 문패는 가릴 수 없다"** — 수신측이 SA를 찾으려면 SPI가 평문이어야 함
- **암호화 범위 "페딩패넥"**: Payload-Padding-PadLength-NextHeader (4개가 암호화 대상)
- **인증 범위는 암호화범위+SPI+SeqNum까지 전부(Authentication Data 자체만 제외)**

## 헷갈리는 포인트
- SPI/Sequence Number가 "인증도 안 된다"고 착각하기 쉬움 — **암호화는 안 되지만 인증(HMAC)은 됨**
- ESP와 AH를 혼동하기 쉬움 — ESP는 기밀성+인증 둘 다, AH는 인증만(기밀성 없음)
- Authentication Data(ICV) 자체는 인증 "대상"이 아니라 인증 "결과값"이라는 점

## 관련 기출/문제
- ESP에서 암호화되지 않는 필드(SPI) 찾기
- SPI의 역할과 왜 평문이어야 하는지
- ESP vs AH 차이(기밀성 제공 여부)
- 필드별 암호화/인증 범위 매칭

## 💬 내 코멘트
- 
