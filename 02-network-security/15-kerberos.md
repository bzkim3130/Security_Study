# Kerberos 인증 프로토콜과 공격기법

## 한줄 요약
> Kerberos는 KDC(AS+TGS)가 발급하는 티켓(TGT→ST)으로 SSO를 구현하며, 이 티켓 발급/검증 과정의 각 단계가 곧 공격기법(Golden/Silver Ticket 등)의 표적이 된다.

## 1. 핵심 구성요소

| 구성요소 | 역할 |
|---|---|
| KDC (Key Distribution Center) | 티켓 발급 센터, AS+TGS로 구성 |
| AS (Authentication Server) | 최초 로그인 인증, TGT 발급 |
| TGS (Ticket Granting Server) | TGT를 받아 서비스용 ST(Service Ticket) 발급 |
| TGT (Ticket Granting Ticket) | "나는 인증됐다"는 증표, TGS에 ST 요청할 때 제시 |
| ST (Service Ticket) | 특정 서비스 접근용 티켓 |

## 2. 인증 흐름 (6단계)

```
1. Client → AS: 로그인 요청 (사전인증: 타임스탬프를 사용자 패스워드해시로 암호화)
2. AS → Client: TGT + 세션키 (krbtgt 계정 해시로 암호화된 TGT)
3. Client → TGS: TGT 제시 + 원하는 서비스 요청
4. TGS → Client: ST (서비스 계정 해시로 암호화)
5. Client → Service Server: ST 제시
6. Service Server: ST 검증 후 서비스 접근 허용
```

## 3. 핵심 개념

- **SSO(Single Sign-On)**: 한번 AS 인증(TGT 발급) 받으면, 이후 서비스마다 재로그인 없이 TGT로 ST를 발급받아 이용
- **사전인증(Pre-authentication)**: AS 요청 시 타임스탬프를 패스워드 해시로 암호화해 전송 → 오프라인 사전공격 방지
- **타임스탬프**: 재전송 공격(Replay Attack) 방지 목적, NTP 동기화 필수
- **왜 AD에서 Kerberos를 NTLM보다 선호하나**: 상호인증 지원, 티켓 기반이라 매번 패스워드 재전송 불필요, 성능/보안 모두 우수

## 4. Kerberos 공격기법 비교

| 공격 | 원리 | 영향 범위 |
|---|---|---|
| Pass-the-Ticket | 탈취한 티켓(TGT/ST)을 재사용 | 티켓 종류에 따라 다름 |
| Pass-the-Hash | NTLM 해시를 직접 사용해 인증 우회 | 계정 단위 |
| Golden Ticket | krbtgt 계정 해시 탈취 → 위조 TGT 생성 | 도메인 전체 (최상위 위협) |
| Silver Ticket | 특정 서비스 계정 해시 탈취 → 위조 ST 생성 | 해당 서비스만 (범위 한정) |
| Kerberoasting | 서비스 계정의 ST를 요청해 오프라인 크래킹 | 서비스 계정 |
| AS-REP Roasting | 사전인증 비활성 계정 대상 오프라인 크래킹 | 해당 계정 |
| Overpass-the-Hash | NTLM 해시로 TGT를 발급받음(Kerberos로 전환) | 계정 단위 |
| Ticket Replay | 탈취한 티켓을 유효기간 내 재사용 | 티켓 유효기간 내 |

## 5. Kerberos v4 vs v5

| 구분 | Kerberos v4 | Kerberos v5 |
|---|---|---|
| 암호화 알고리즘 | DES 고정 | 다양한 알고리즘 지원(알고리즘 독립적) |
| 주소 체계 | IP주소만 지원 | 다양한 네트워크 주소 지원 |
| 티켓 유효기간 | 최대 21시간(5분×2⁸ 제한) | 임의 지정 가능(시작/종료시간 명시) |
| 티켓 갱신(Renewal) | 미지원 | 지원 (Renewable ticket) |
| 위임(Delegation) | 미지원 | 지원 (Forwardable/Proxiable ticket) |
| 영역 간 인증 (Cross-realm) | 제한적, N×N 구조 | 계층적 구조로 개선 |
| 메시지 인코딩 | 자체 정의 방식 | ASN.1 (표준 인코딩) |
| 이중 암호화 | 티켓 이중 암호화 존재(비효율) | 이중 암호화 제거 |
| PKI 연동 | 미지원 | PKINIT 확장으로 지원 가능 |

```
Kerberos v4
 └─ DES only, IP주소만, 21H 제한, 위임 불가
        │
        ▼  (개선)
Kerberos v5
 └─ 알고리즘 자유, 다양한 주소, 유효기간 자유
    +Renewable +Forwardable +ASN.1 +계층적 cross-realm
```

**암기**: "4는 낡고 갇혀있다(DES/IP/21시간 제한), 5는 넓고 자유롭다(알고리즘·주소·시간 자유+위임·갱신)"

## 암기 팁
- **"Golden=도메인 전체(krbtgt), Silver=서비스 하나(서비스계정)"** — 범위 크기로 구분
- **Kerberoasting = "정상 기능(ST 요청)을 악용하는 것"**, 취약점을 뚫는 게 아님 — 그래서 탐지가 어려움
- 흐름 순서: **"AS(첫인증)→TGT→TGS(서비스요청)→ST→서비스접근"**
- v4/v5 구분: **"DES·IP·21시간 나오면 v4"**

## 헷갈리는 포인트
- TGT는 "인증됐다는 증표", ST는 "이 서비스 써도 된다는 증표" — 발급 기관도 다름(TGT=AS, ST=TGS)
- Golden Ticket과 Silver Ticket은 둘 다 "위조 티켓"이지만 **탈취하는 해시의 대상이 다름** (krbtgt vs 서비스계정) → 영향 범위가 완전히 다름
- **"이중 암호화 제거"는 v5의 개선점** — v4에서는 티켓을 두 번 암호화해서 비효율적이었음
- **PKINIT은 v5의 확장 기능**(공개키 기반 초기인증) — v4엔 없음

## 관련 기출/문제
- Kerberos 인증 6단계 순서
- Golden Ticket vs Silver Ticket 영향범위 차이
- Kerberoasting이 "취약점 공격이 아닌 정상기능 악용"인 이유
- "Kerberos v5의 특징이 아닌 것은?" → DES 고정/IP주소만/21시간 제한이 보기로 나오면 v4 특징(오답)

## 💬 내 코멘트
- 
