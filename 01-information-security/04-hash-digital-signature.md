# 해시함수 / 전자서명 - SHA 알고리즘 비교

## 한줄 요약
> SHA-1은 낡았고, SHA-2는 현재 표준이며, SHA-3는 구조 자체가 다른 스펀지(Sponge) 방식이다.

## 1. SHA 계열 비교표

| 알고리즘 | 출력 길이 | 구조 | 보안 상태 |
|---|---|---|---|
| SHA-1 | 160비트 | Merkle-Damgård | 취약 (충돌 공격 발견됨) |
| SHA-224 | 224비트 | Merkle-Damgård | 안전 |
| SHA-256 | 256비트 | Merkle-Damgård | 안전 (현재 표준) |
| SHA-384 | 384비트 | Merkle-Damgård | 안전 |
| SHA-512 | 512비트 | Merkle-Damgård | 안전 |
| SHA-3 | 가변 | Sponge (스펀지) | 안전, Keccak 기반 |

## 2. 구조 차이: Merkle-Damgård vs Sponge

**SHA-1, SHA-2 계열**: Merkle-Damgård 구조
- 입력을 블록 단위로 나눠 순차적으로 압축함수 통과 → 이전 블록 결과가 다음 블록 입력에 영향

**SHA-3**: Sponge(스펀지) 구조
- Keccak 알고리즘 기반
- 데이터를 "흡수(absorb)"했다가 "짜내는(squeeze)" 방식으로 해시값 생성
- SHA-1/SHA-2와 완전히 다른 설계 철학

## 암기 팁
- **"SHA-1은 낡았다, SHA-2는 표준이다, SHA-3는 스펀지다"**
- SHA-1 = 충돌 공격 취약 → 시험에서 "이제 안 쓰는 것" 나오면 SHA-1
- SHA-3만 유일하게 Merkle-Damgård가 아닌 Sponge 구조 → 구조 문제 나오면 SHA-3부터 체크

## 헷갈리는 포인트
- SHA-2와 SHA-3는 "숫자만 다음"이 아니라 **내부 구조 자체가 다름** (SHA-2는 SHA-1의 확장판 개념, SHA-3는 완전히 새로운 설계)
- SHA-3 = Keccak 그 자체가 아니라, Keccak을 표준화한 것

## 관련 기출/문제
- 각 알고리즘 출력 길이 암기 문제
- "Merkle-Damgård 구조를 쓰지 않는 것은?" → SHA-3
