# Windows 감사 정책 (Audit Policy)

## 한줄 요약
> Windows 감사 정책은 기본 9종(Basic)과 고급 10개 범주/53개 하위범주(Advanced)로 나뉘며, 둘을 동시에 설정하면 충돌이 날 수 있다.

## 1. Basic Audit Policy 9종 (상세 이벤트ID + 기록위치)

| 종류 | 설명 | 주요 이벤트ID | 기록 위치 |
|---|---|---|---|
| 계정 로그온 이벤트 (Account Logon Events) | **도메인 컨트롤러**에서의 인증(Kerberos/NTLM 검증) | 4768(TGT발급), 4769(ST발급), 4771(사전인증실패), 4776(NTLM검증) | **DC** |
| 로그온 이벤트 (Logon Events) | **로컬 시스템**에서의 실제 로그온 | 4624(성공), 4625(실패), 4634(로그오프), 4648(다른자격증명으로 로그온) | **로컬 컴퓨터** |
| 계정 관리 (Account Management) | 계정 생성/수정/삭제 | 4720(생성), 4722(활성화), 4724(암호재설정), 4726(삭제) | 계정 변경이 일어난 서버 |
| 디렉터리 서비스 액세스 | AD 객체 접근 | 4662 | DC |
| 개체 액세스 (Object Access) | 파일/폴더 등 개체 접근 (SACL 설정 필요) | 4663(접근시도), 4656(핸들열기) | 로컬 컴퓨터 |
| 정책 변경 (Policy Change) | 보안정책 변경 | 4719(감사정책변경) | 정책 변경이 일어난 서버 |
| 권한 사용 (Privilege Use) | 특수 권한 사용 | 4672(특별권한로그온), 4673 | 로컬 컴퓨터 |
| 프로세스 추적 (Process Tracking) | 프로세스 생성/종료 | 4688(프로세스생성), 4689(프로세스종료) | 로컬 컴퓨터 |
| 시스템 이벤트 (System Events) | 시스템 시작/종료 등 | 1102(로그삭제), 4608(시스템시작) | 로컬 컴퓨터 |

## 2. 계층 구조

```
Windows 감사 정책
├── Basic Audit Policy (9종, 단순)
└── Advanced Audit Policy (10개 범주, 53개 하위범주, 세밀)
      예: 로그온/로그오프 범주 안에 로그온, 로그오프, 계정잠금 등 하위항목
```

## 3. 핵심 구분 포인트

**Account Logon Events vs Logon Events — Kerberos 흐름으로 이해**

```
[사용자 PC] ── 로그온 시도 ──▶ [로컬 시스템]
      │                              │
      │ Kerberos 인증 요청            │ 4624/4625 기록(Logon Events)
      ▼                              
   [도메인 컨트롤러(DC)]
      │
      │ TGT/ST 발급·검증
      ▼
   4768/4769/4771 기록(Account Logon Events)
```
- **Account Logon Events**: "신분증(TGT/ST) 발급소"에서 일어나는 일 → **DC에 기록**
- **Logon Events**: "실제 문 열고 들어가는" 로컬 행위 → **로컬 컴퓨터에 기록**

**Object Access 감사의 함정**
- 감사 정책에서 Object Access를 활성화하는 것만으로는 부족 — **해당 파일/폴더에 SACL(시스템 접근 제어 목록)도 별도로 설정해야** 실제 로그가 남음

**Basic vs Advanced 동시 사용 시 충돌**
- 둘 다 설정하면 예상과 다른 로그 결과가 나올 수 있음 → 보통 Advanced 사용 시 Basic은 비활성화 권장

## 암기 팁
- **"로계개정 권프시디"** — 로그온이벤트-계정관리-개체액세스-정책변경-권한사용-프로세스추적-시스템이벤트... (9종 앞글자로 구성한 니모닉)
- **"어카운트 로그온 = 도메인 컨트롤러, 로그온 이벤트 = 내 PC"** — Account가 붙으면 DC쪽
- **"Object Access 켜는 것 + SACL 설정 = 세트"** — 하나만 하면 로그 안 남음

## 헷갈리는 포인트
- Account Logon Events와 Logon Events는 이름이 비슷해서 헷갈리기 쉬움 — **어디서 발생하는지(DC vs 로컬)**로 구분
- Basic과 Advanced를 동시에 켜두면 로그 기록이 꼬일 수 있음 — 둘 중 하나로 통일 권장

## 관련 기출/문제
- Basic Audit Policy 9종 이름 암기
- Account Logon Events vs Logon Events 구분
- Object Access 감사에 SACL이 필요한 이유
- 주요 이벤트ID(4624, 4625, 4688 등) 매칭

## 💬 내 코멘트
- 
