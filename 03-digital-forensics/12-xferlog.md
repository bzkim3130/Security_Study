# xferlog (FTP 전송 로그)

## 한줄 요약
> xferlog는 wu-ftpd/vsftpd 같은 FTP 데몬이 남기는 전송 기록이며, 14개 필드 중 방향필드(i/o/d)가 데이터 유출(exfiltration) 여부를 판단하는 포렌식 핵심 포인트다.

## 1. xferlog 필드 구조 (14개 필드)

```
current-time transfer-time remote-host file-size filename
transfer-type special-action-flag direction access-mode
username service-name authentication-method authenticated-user-id
completion-status
```

| # | 필드명 | 의미 | 예시 |
|---|---|---|---|
| 1 | current-time | 전송 시각 | Mon Aug 16 13:20:00 2026 |
| 2 | transfer-time | 전송 소요 시간(초) | 3 |
| 3 | remote-host | 원격 호스트(클라이언트) IP/도메인 | 192.168.1.10 |
| 4 | file-size | 전송된 파일 크기(byte) | 10240 |
| 5 | filename | 전송된 파일 경로 | /home/user/data.zip |
| 6 | transfer-type | 전송 타입(a=ASCII, b=binary) | b |
| 7 | special-action-flag | 압축/tar 등 특수 처리 (C/U/T/_) | _ |
| 8 | direction | **전송 방향** (i=incoming/업로드, o=outgoing/다운로드) | o |
| 9 | access-mode | 접속 방식(a=익명, g=guest, r=인증사용자) | r |
| 10 | username | 사용자명(로컬) | bzkim |
| 11 | service-name | 서비스명 | ftp |
| 12 | authentication-method | 인증방법(0=none, 1=RFC931) | 0 |
| 13 | authenticated-user-id | 인증된 사용자ID | * |
| 14 | completion-status | 완료 상태(c=complete, i=incomplete) | c |

## 2. 필드 그룹별 분류

```
시간 그룹:   current-time, transfer-time
호스트 그룹: remote-host
파일 그룹:   file-size, filename, transfer-type
방향 그룹:   special-action-flag, direction  ★ 포렌식 핵심
인증 그룹:   access-mode, username, authentication-method, authenticated-user-id
결과 그룹:   completion-status
```

## 3. 포렌식 활용 포인트

- **direction 필드(i/o)**: 침해사고 조사에서 가장 중요 — **o(outgoing, 다운로드)**가 업무시간 외 대용량으로 발생하면 **데이터 유출(exfiltration) 정황**으로 의심
- **completion-status(c/i)**: 전송이 끝까지 완료됐는지(c) 중간에 끊겼는지(i) — 파일 무결성 판단에 활용
- **remote-host + username 조합**: 누가 어디서 접속해 파일을 주고받았는지 특정

## 암기 팁
- **필드 순서 니모닉**: "시간-소요-호스트-크기-파일명-타입-특수-방향-접속방식-사용자-서비스-인증방법-인증ID-완료상태"
- **direction: "i는 들어옴(in=업로드), o는 나감(out=다운로드)"**
- **"o(다운로드)+대용량+새벽시간 = 유출 의심 조합"**

## 헷갈리는 포인트
- direction의 i/o는 **서버 기준**임 — i(incoming)는 클라이언트가 서버로 업로드, o(outgoing)는 서버에서 클라이언트로 다운로드(서버 입장에서 나가는 것)
- completion-status의 i(incomplete)를 direction의 i(incoming)와 혼동하기 쉬움 — 완전히 다른 필드, 다른 의미

## 관련 기출/문제
- xferlog 14개 필드 순서
- direction 필드(i/o)의 의미와 포렌식 활용
- 데이터 유출 의심 정황 판단(direction+file-size+시간대)

## 💬 내 코멘트
-
```
Mon Aug 17 14:23:01 2026 3 192.168.0.5 10240 /home/bzkim/secret.zip b _ o r bzkim ftp 0 * c
```
해석 한 줄 요약
"2026-08-17 14:23:01, bzkim 계정이 192.168.0.5에서 접속해서 secret.zip(10KB)을 바이너리 모드로 3초 만에 다운로드(outgoing) 완료함"

업로드(incoming) 예시도 비교해보기
Mon Aug 17 15:10:22 2026 5 192.168.0.5 20480 /home/bzkim/upload.zip b _ i r bzkim ftp 0 * c
차이점	outgoing(위 예시)	incoming(이 예시)
direction	o	i
의미	서버 → 외부 (다운로드)	외부 → 서버 (업로드)
포렌식 관점	정보 유출 의심	악성코드/파일 반입 의심
시험 포인트 재확인

필드 순서 자체를 묻기보다, "이 로그에서 direction 필드 값이 뭐고 무슨 의미냐" 식으로 특정 필드 하나 콕 짚어서 해석시키는 문제가 많음 → direction(⑧)과 access-mode(⑨) 위치 순서, b/a(transfer-type)와 c/i(completion-status) 구분을 정확히 익혀두는 게 중요.
