# VPN / TLS / SSL - TLS 핸드셰이크와 키 유도

## 한줄 요약
> TLS는 비대칭키로 세션키를 교환(느림)하고 이후 대칭키로 실제 데이터를 암호화(빠름)하는 하이브리드 방식이며, TLS 1.3은 이 과정을 1-RTT(재접속시 0-RTT)로 줄이고 취약한 옛날 알고리즘을 다 제거했다.

## 0. TLS 기본 구조

| 구분 | 내용 |
|---|---|
| 계층 | OSI 4~5계층 사이 (Transport~Session) |
| 목적 | 기밀성(암호화), 무결성(MAC/AEAD), 인증(인증서) |
| 대칭키 | 세션키로 실제 데이터 암호화 (빠름) |
| 비대칭키 | 세션키 교환/인증에 사용 (느림, 초기 협상만) |
| 버전 흐름 | SSL 2.0/3.0(폐기) → TLS 1.0/1.1(폐기) → TLS 1.2 → **TLS 1.3(현행 권장)** |

## 1. TLS 1.2 핸드셰이크 (4-way, 2-RTT)

```
Client                                   Server
  |-- 1.ClientHello ------------------->|  (지원 암호스위트, ClientRandom)
  |<-- 2.ServerHello --------------------|  (선택된 암호스위트, ServerRandom)
  |<-- Certificate -----------------------|  (서버 인증서, 평문!)
  |<-- ServerHelloDone --------------------|
  |-- 3.ClientKeyExchange -------------->|  (PreMasterSecret 암호화 전달)
  |-- ChangeCipherSpec + Finished ------>|
  |<-- 4.ChangeCipherSpec + Finished -----|
  (이후 세션키로 암호화된 데이터 통신)
```

## 2. TLS 1.3 핸드셰이크 (1-RTT)

```
클라이언트                          서버
   |-- ClientHello + KeyShare ------>|
   |<-- ServerHello + KeyShare -------|
   |<-- {Certificate, Finished} ------|  (암호화됨)
   |-- {Finished} ------------------->|
   |<====== 암호화된 데이터 교환 ======>|
```
클라이언트가 처음부터 키 교환 정보(KeyShare)를 함께 보내기 때문에 왕복 횟수가 줄어듦.

## 3. TLS 1.2 vs 1.3 비교

| 항목 | TLS 1.2 | TLS 1.3 |
|---|---|---|
| RTT | 2-RTT | 1-RTT (0-RTT 재접속 지원) |
| 키 교환 | RSA 키교환 가능 | RSA 키교환 제거, ECDHE만 허용(PFS 강제) |
| 암호 스위트 | 다양(RC4, DES 등 취약한 것 포함) | 취약 알고리즘 전면 제거, AEAD만 허용 |
| Handshake 암호화 | ServerHello 이후 평문 구간 존재(인증서 평문) | Certificate부터 암호화 |
| 압축 | 지원(CRIME 공격 취약점) | 압축 제거 |
| 재협상(Renegotiation) | 가능(취약점 있음) | 제거 |

## 4. 마스터 비밀(Master Secret) & 의사난수함수(PRF)

**핵심 재료**

| 재료 | 역할 |
|---|---|
| PreMasterSecret | 키교환 직후 나온 값(방식별로 생성법 다름) |
| ClientRandom / ServerRandom | 매 세션마다 새로 생성되는 난수(평문 전송, 재전송 공격 방지+매번 다른 키 생성용) |
| PRF (Pseudo-Random Function) | 입력값들을 받아 세션키(암호화키, MAC키, IV 등)를 필요한 만큼 생성하는 함수 |

**PreMasterSecret 생성 방식**
- RSA 방식: 클라이언트가 랜덤 생성 → 서버 공개키로 암호화해서 전송
- DHE/ECDHE 방식: 양쪽이 각자 계산해서 동일한 값 도출(전송 안 함 → PFS 보장)

**키 유도 3단계**
```
PreMasterSecret ─┐
ClientRandom ────┼─ PRF → MasterSecret(48바이트, 고정길이)
ServerRandom ────┘           │
                              ├─ PRF 재적용 ─→ Key Block 생성
                              │
                    Client/Server Write MAC Key
                    Client/Server Write Encryption Key
                    IV (필요시)
```

**TLS 1.2 vs 1.3 키유도 함수 명칭**: TLS 1.2는 PRF(HMAC 기반), TLS 1.3은 **HKDF**(HMAC-based Key Derivation Function)로 명칭이 바뀜

## 5. 0-RTT 주의점

재접속 시 즉시 데이터를 보낼 수 있어 빠르지만, **재전송 공격(replay attack)**에 취약할 수 있음 → 멱등성이 보장되는 요청(GET 등)에만 사용 권장.

## 암기 팁
- **"1.3은 다 빼고 빠르게"** → RSA 키교환 ❌, 압축 ❌, 재협상 ❌, 대신 1-RTT로 빠름
- RTT 숫자로 버전 연결: **"1.2=2번 왕복, 1.3=1번 왕복"** (숫자가 거꾸로라 헷갈림 주의)
- PFS = "매번 새 키" → TLS 1.3에서 강제됨(개인키 유출돼도 과거 세션 안전)
- **"Pre → Master → Key" 3단계 유도 순서**: 재료(PreMaster) → 원액(Master) → 소분(Key Block)
- PRF는 **"키 뽑는 기계"** — 입력이 달라도 항상 필요한 길이만큼 출력 가능

## 헷갈리는 포인트
- SSL vs TLS: SSL은 폐기된 구버전, TLS가 후속 표준 (시험에서 관용적으로 "SSL/TLS" 묶어서 출제되기도 함)
- 대칭키 vs 비대칭키 사용 순서: 처음엔 비대칭(키교환/인증), 이후엔 대칭(데이터 암호화) — 순서 뒤바꿔 출제되는 오답 많음
- **PreMasterSecret ≠ MasterSecret**: PreMaster는 키교환 직후 값(가변적), Master는 PRF를 거친 **고정 48바이트** — 헷갈려서 바꿔 출제됨
- PRF의 역할은 "암호화"가 아니라 **"키 유도(Key Derivation)"** — MAC/대칭암호와 혼동 주의
- **Random 값은 평문으로 전송됨**(ClientHello/ServerHello 단계) — "Random도 암호화되어 전송된다"는 오답 함정 주의

## 관련 기출/문제
- TLS 1.2 vs 1.3 차이점 비교 문제
- TLS 1.3에서 제거된 알고리즘 목록
- 0-RTT의 보안 취약점
- PreMasterSecret과 MasterSecret 구분
- PRF/HKDF의 역할(암호화 아닌 키유도)

## 💬 내 코멘트
- 

