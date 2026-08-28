# FTP 명령어 정리 (클라이언트 명령어 vs 프로토콜 명령어)

## 한줄 요약
> FTP 명령어는 사람이 입력하는 "클라이언트 명령어"(get, put 등)와 실제로 서버에 전송되는 "프로토콜 명령어"(RETR, STOR 등) 2단계로 나뉘며, get→RETR, put→STOR로 자동 변환된다.

## 1. 두 종류 명령어 구분

| 구분 | 설명 | 예시 |
|---|---|---|
| ① 클라이언트 사용자 명령어 | 사람이 FTP 프롬프트에 직접 타이핑 | get, put, ls, cd |
| ② 프로토콜 명령어(내부 전송) | ①을 서버로 보낼 때 실제 변환되어 전송(RFC 959) | RETR, STOR, LIST, CWD |

## 2. 클라이언트 명령어 (사용자 입력)

| 명령어 | 기능 |
|---|---|
| open | 서버 접속 |
| close / disconnect | 접속 종료(세션만) |
| bye / quit | 프로그램 종료 |
| user | 사용자 로그인 |
| ls / dir | 파일 목록 보기 |
| cd | 원격 디렉터리 이동 |
| lcd | **로컬** 디렉터리 이동 |
| pwd | 현재 원격 디렉터리 확인 |
| get | 파일 1개 다운로드 |
| mget | 파일 여러개 다운로드 |
| put | 파일 1개 업로드 |
| mput | 파일 여러개 업로드 |
| delete | 원격 파일 삭제 |
| mkdir / rmdir | 디렉터리 생성/삭제 |
| rename | 파일명 변경 |
| binary / ascii | 전송모드 설정 |

## 3. 프로토콜 명령어 (실제 전송, RFC 959)

| 명령어 | 기능 |
|---|---|
| USER / PASS | 계정/비밀번호 |
| LIST / NLST | 파일목록(LIST=상세, NLST=이름만) |
| RETR | 서버→클라이언트 다운로드 |
| STOR | 클라이언트→서버 업로드 |
| STOU | 고유 파일명으로 업로드 |
| APPE | 파일 이어붙이기 |
| DELE | 파일 삭제 |
| RNFR / RNTO | 파일명 변경(from/to, 항상 쌍) |
| MKD / RMD | 디렉터리 생성/삭제 |
| CWD | 디렉터리 이동 |
| PWD | 현재 디렉터리 출력 |
| PORT | 액티브 모드 요청 |
| PASV | 패시브 모드 요청 |
| QUIT | 접속 종료 |

## 4. 사용자 명령 → 프로토콜 명령 매핑

```
get     → RETR
put     → STOR
ls/dir  → LIST/NLST
cd      → CWD
delete  → DELE
bye/quit → QUIT
```

## 5. Active vs Passive 모드

```
[Active Mode (PORT)]
Client → 제어연결(TCP 21) → Server
Client → PORT명령(내 포트 알림) → Server
Server → 20번 포트로 클라이언트에 접속(데이터연결)

[Passive Mode (PASV)]
Client → 제어연결(TCP 21) → Server
Client → PASV명령 → Server
Server → 임의포트 알림 → Client
Client → 그 포트로 접속(데이터연결)
```

- **PORT(액티브)**: 서버가 클라이언트로 먼저 연결 시도 → 클라이언트 방화벽에 막히기 쉬움
- **PASV(패시브)**: 클라이언트가 먼저 연결 시도 → 방화벽 통과 잘 됨(요즘 대부분 이 방식)

## 암기 팁
- **"R은 Read처럼 받는거, S는 Store처럼 저장(업로드)"** → RETR(다운로드)/STOR(업로드)
- **포트 21=제어(명령), 포트 20=데이터(액티브 모드 한정)**
- **lcd = Local Change Directory** — "l"만 보면 로컬!
- **mget/mput의 m = multiple**
- **PORT/PASV 구분: "누가 먼저 연결을 시도하는가"**

## 헷갈리는 포인트
- 시험에서 "FTP 명령어"라고만 물으면 어느 걸 묻는지 문맥으로 파악해야 함 — 보기에 RETR/STOR면 프로토콜, get/put이면 클라이언트 명령어
- close는 세션만 종료(재접속 가능), quit/bye는 프로그램 자체 종료
- **20번 포트는 액티브 모드에서만 사용**됨 — 패시브 모드는 서버가 알려준 임의 포트 사용
- FTP는 기본적으로 **평문 전송** → FTPS(SSL/TLS 적용) 또는 SFTP(SSH 기반, 별개 프로토콜)와 비교 출제

## 관련 기출/문제
- get/put ↔ RETR/STOR 매핑 문제
- PORT vs PASV 동작 순서
- close vs quit/bye 차이

## 💬 내 코멘트
- 
