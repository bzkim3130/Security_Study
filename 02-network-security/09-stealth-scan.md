# 스텔스 스캔 (Stealth Scan)

## 한줄 요약
> TCP 3-way handshake를 완전히 끝내지 않고 포트 상태를 확인하는 기법으로, 연결 기록이 로그에 잘 안 남아 탐지를 회피한다.

## 1. 개요

스텔스 스캔은 대상 시스템과 정상 연결(3-way handshake)을 완료하지 않은 채 포트의 열림/닫힘 상태를 확인하는 포트 스캔 기법. 정상 연결이 아니므로 애플리케이션 로그에 잘 남지 않아 탐지 회피에 유리함.

## 2. 동작 원리 (SYN Scan 기준, 대표적인 스텔스 스캔)

**정상 연결(3-way handshake)**
```
Client → Server : SYN
Server → Client : SYN/ACK
Client → Server : ACK   (연결 완료)
```

**SYN 스캔 (Half-open Scan)**
```
Client → Server : SYN
Server → Client : SYN/ACK (포트 열림) 또는 RST (포트 닫힘)
Client → Server : RST     (ACK 대신 RST로 강제 종료)
```

- 포트 열림: SYN/ACK 응답 받고, ACK 대신 **RST**로 연결 강제 종료
- 포트 닫힘: RST/ACK가 즉시 응답으로 옴
- 필터링(방화벽 차단): 응답 없음 또는 ICMP unreachable

## 3. 주요 스텔스 스캔 종류

| 스캔 기법 | 사용 플래그 | 특징 |
|---|---|---|
| SYN Scan | SYN | 가장 일반적, Half-open |
| FIN Scan | FIN | 닫힌 포트=RST, 열린 포트=무응답(RFC793 기준) |
| NULL Scan | 플래그 없음 | FIN Scan과 동일 원리 |
| Xmas Scan | FIN+PSH+URG | 플래그를 다 세팅, 동일 원리 |
| ACK Scan | ACK | 방화벽 필터링 규칙(stateful/stateless) 판별용 |
| Maimon Scan | FIN/ACK | 일부 BSD 계열 스택 특성 이용 |

> FIN/NULL/Xmas 스캔은 Windows 계열에서는 RFC 규격을 안 따라 정확도가 떨어지는 경우가 많음

## 4. 탐지 회피 이유 & 탐지 방법

**왜 회피가 되나**
- 3-way handshake 미완료 → 애플리케이션 로그에 연결 기록 안 남음
- 스캐너는 raw socket 사용 (관리자 권한 필요)

**어떻게 탐지하나**
- 짧은 시간 내 다수 SYN만 있고 ACK로 안 이어지는 패턴 탐지 (Half-open 연결 급증)
- 방화벽/IDS에서 SYN 대비 완료 연결 비율 모니터링
- Snort/Suricata의 포트스캔 탐지 룰 (짧은 시간 내 다수 포트로 SYN)
- NetFlow/sFlow 기반 비정상 SYN 세션 분석

## 5. 대표 도구

```
nmap -sS <target>   # SYN Scan
nmap -sF <target>   # FIN Scan
nmap -sN <target>   # NULL Scan
nmap -sX <target>   # Xmas Scan
nmap -sA <target>   # ACK Scan
```

## 암기 팁
- **"SYN 스캔 = 문 두드리다가 열리면 바로 도망(RST)"** — 정식으로 안 들어감(ACK 안 보냄)
- **FIN/NULL/Xmas는 삼형제**: 원리 같고 플래그 조합만 다름
- **"Windows는 RFC 규격을 안 지켜서 FIN/NULL/Xmas가 잘 안 먹힘"**

## 헷갈리는 포인트
- SYN 스캔은 "완전한 스캔이 아니다"라고 해서 탐지가 아예 불가능한 건 아님 — SYN 패킷 패턴 자체로 탐지 가능
- ACK Scan은 포트의 열림/닫힘이 아니라 **방화벽 필터링 규칙 여부**를 알아내는 용도 (다른 스캔들과 목적이 다름)

## 관련 기출/문제
- SYN Scan 동작 순서 (SYN→SYN/ACK→RST)
- FIN/NULL/Xmas 스캔 원리와 Windows에서 부정확한 이유
- ACK Scan의 목적 (포트상태 아닌 필터링 규칙 확인)

## 💬 내 코멘트
- 
