# PKI / 인증서 (CA 구조 + CRL 필드)

## 한줄 요약
> PKI는 사용자 → RA(신원확인) → CA(발급) → 저장소(보관) → VA(검증) 순서로 신뢰를 전달하는 구조다.

## 1. PKI 구성 기관

| 기관 | 역할 | 한 줄 요약 |
|---|---|---|
| Root CA (최상위 인증기관) | 계층 구조 최상단, 하위 CA에 인증서 발급 | "내가 보증한다"의 시작점 (신뢰의 근원) |
| CA (인증기관) | 인증서 발급·서명·폐기 | PKI의 핵심 발급 주체 |
| RA (등록기관) | 사용자 신원 확인 후 CA에 발급 요청 중계 | CA의 업무 부담을 분산 |
| 저장소 (Repository) | 인증서·CRL 공개 저장 | 누구나 조회 가능한 창고 |
| VA (검증기관) | 실시간 유효성 검증 (OCSP) | "지금 이 인증서 살아있나?" 확인 |
| TSA (타임스탬프기관) | 전자문서 특정 시점 존재 증명 | 전자서명 부인방지에 활용 |

## 2. 흐름도

```
[사용자] → 신원확인 요청 → [RA]
                              │ 발급 요청
                              ▼
                            [CA] ──인증서 발급──▶ [저장소]
                              │                      │
                         폐기 정보 전달           (인증서·CRL 보관)
                              ▼                      │
                            [VA] ◀────────────────────┘
                              ▲
                    유효성 확인 요청
                              │
                        [서비스 이용자]

[TSA]: 서명 시점 증명 (부인방지용, 별도 동작)
```

**핵심 흐름**: 사용자 → RA(신원확인) → CA(발급) → 저장소(보관) → VA(검증)

## 3. CRL (Certificate Revocation List) 필드

CRL = "폐기된 인증서 명단표". CA가 "얘내들은 이제 못 믿어"라고 공지하는 문서.

### 기본 필드 (Basic Fields)

```
┌─────────────────────────────────────┐
│  CRL 기본 구조                        │
├─────────────────────────────────────┤
│ Version          버전 (보통 v2)       │
│ Signature Algo   서명 알고리즘        │
│ Issuer           발급자(CA) 정보      │
│ This Update      이번 발행일          │
│ Next Update      다음 발행 예정일     │
│ Revoked Certs    폐기된 인증서 목록    │
│   ├ 일련번호(Serial Number)          │
│   └ 폐기일자(Revocation Date)        │
│ Signature        CA의 전자서명        │
└─────────────────────────────────────┘
```

### 확장 필드 (Extension Fields, v2부터)

**CRL 전체에 적용**
- Authority Key Identifier (AKI): CA를 식별하는 키 정보
- CRL Number: CRL 순번 (증가하는 일련번호)
- Issuing Distribution Point: 이 CRL이 다루는 범위/용도

**개별 폐기 항목에 적용**
- Reason Code: 폐기 사유 (키 손상, 정지 등)
- Invalidity Date: 실제 문제(무효화) 발생 시점
- Certificate Issuer: (간접 CRL일 때) 원 발급자 CA

## 암기 팁
- **PKI 기관**: "사용자→RA→CA→저장소→VA" 순서로 스토리텔링
- **CRL 기본 필드**: "버.서.발.이.다.폐.서" = 버전→서명알고리즘→발급자→이번발행일→다음발행일→폐기목록→서명
- **CRL 확장 필드**: "확장은 두 층" — 전체용(AKI/Number/IDP = 누가·몇번째·어디까지) vs 개별용(Reason/Invalidity Date = 왜·언제)

## 헷갈리는 포인트
- **This Update vs Next Update**: This Update = 이 CRL 발행 시점(지금 기준 최신), Next Update = 다음 CRL 예정 시점(이때까지는 믿어도 됨)
- **Revocation Date vs Invalidity Date**: Revocation Date = CA가 "폐기 처리한" 행정일, Invalidity Date = 실제로 문제(키 유출 등)가 생긴 사고 발생일 → 보통 **Invalidity Date가 더 이전** (사고 나고 → 나중에 CA가 처리)

## 관련 기출/문제
- PKI 구성 기관별 역할 매칭 문제
- CRL 기본/확장 필드 구분 문제
- Revocation Date와 Invalidity Date 선후관계 문제
