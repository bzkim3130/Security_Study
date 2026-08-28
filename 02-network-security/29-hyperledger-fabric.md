# Hyperledger와 Hyperledger Fabric

## 한줄 요약
> Hyperledger는 리눅스재단이 여러 블록체인 프로젝트(Fabric, Sawtooth 등)를 모아둔 우산 조직이고, Fabric은 그 중 가장 대표적인 "허가형(Permissioned) 블록체인" 플랫폼이다.

## 1. Hyperledger 프로젝트 구조

```
Hyperledger (리눅스재단 산하 우산 프로젝트)
├── Fabric      — IBM 주도, 가장 널리 쓰이는 허가형 블록체인
├── Sawtooth    — Intel 주도
├── Besu        — 이더리움 호환 엔터프라이즈 블록체인
├── Indy        — 분산신원(DID) 특화
└── Iroha       — 모바일/IoT 특화
```

## 2. 공개 블록체인 vs 허가형(Permissioned) 블록체인

| 구분 | 공개(Public) 블록체인 | 허가형(Permissioned) 블록체인 |
|---|---|---|
| 참여 | 누구나 가능 | **승인된 참여자만** |
| 대표 예 | 비트코인, 이더리움 | Hyperledger Fabric |
| 합의 알고리즘 | PoW, PoS | Raft, Kafka (효율적 합의) |
| 익명성 | 익명/가명 | 신원 확인됨(MSP 기반) |
| 처리속도 | 상대적으로 느림 | 빠름(참여자 제한적이라) |

## 3. Fabric 핵심 구성요소

| 구성요소 | 역할 |
|---|---|
| Chaincode | Fabric의 스마트컨트랙트(비즈니스 로직 구현) |
| MSP (Membership Service Provider) | 참여자의 신원·자격 관리 |
| Ordering Service | 트랜잭션 순서 정렬(합의 담당) |
| Channel | 특정 참여자 그룹만의 프라이빗 원장 분리 |
| Peer | 원장 저장, 트랜잭션 검증·실행 |

## 암기 팁
- **"Hyperledger = 우산, Fabric = 그 우산 아래 가장 유명한 우산살"**
- **"허가형은 신분증 검사(MSP), 공개형은 아무나 입장"**
- **Chaincode = "이더리움의 스마트컨트랙트에 해당하는 Fabric 용어"**

## 헷갈리는 포인트
- Hyperledger는 "특정 블록체인 하나"가 아니라 **여러 프로젝트를 모은 재단/우산 조직** — Fabric과 동일시하면 안 됨
- Fabric은 코인 채굴(PoW) 개념이 없음 — 참여자가 이미 신뢰된 상태(허가형)라 Raft/Kafka 같은 효율적 합의만 사용

## 관련 기출/문제
- Hyperledger 산하 프로젝트 종류
- 공개형 vs 허가형 블록체인 차이
- Fabric의 핵심 구성요소(Chaincode/MSP/Ordering Service/Channel) 역할 매칭

## 💬 내 코멘트
- 
