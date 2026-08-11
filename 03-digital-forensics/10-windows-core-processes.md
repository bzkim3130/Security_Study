# Windows 기본 프로세스 부팅 순서와 부모-자식 관계

## 한줄 요약
> 부팅 시 정해진 순서(System→smss→wininit/winlogon→services/lsass→svchost)로 프로세스가 생성되며, 이 "정상 부모-자식 관계"를 알아야 악성코드 위장(가짜 svchost.exe 등)을 탐지할 수 있다.

## 1. 핵심 프로세스 (부팅 순서대로)

| 프로세스 | 역할 | 정상 부모 | 비고 |
|---|---|---|---|
| System (PID 4) | 커널 모드 스레드 컨테이너 | 없음(최상위) | PID 항상 4 |
| smss.exe | Session Manager, 초기 세션 생성 | System | 자식 실행 후 종료(세션0 제외) |
| csrss.exe | Client/Server Runtime, Win32 서브시스템 | smss.exe(실행 시점만) | 부모 종료돼도 정상 |
| wininit.exe | 세션0(서비스) 초기화 | smss.exe | - |
| winlogon.exe | 로그온 세션(세션1+) 관리, Ctrl+Alt+Del | smss.exe | - |
| services.exe | SCM(Service Control Manager) | wininit.exe | 서비스 시작 담당 |
| lsass.exe | 로그온 인증, 보안정책, 자격증명 저장 | wininit.exe | **해킹 표적 1순위** |
| svchost.exe | 여러 DLL 서비스 호스팅 | services.exe | **위장 악성코드 최다 발생** |
| explorer.exe | 사용자 셸(바탕화면) | userinit.exe → 종료 후 고아 | 부모 없어도 정상 |

## 2. 프로세스 트리 도식화

```
System (PID 4)
  └── smss.exe (Session Manager)
        ├── csrss.exe (세션0)
        ├── wininit.exe (세션0 초기화)
        │     ├── services.exe (SCM)
        │     │     └── svchost.exe (서비스 호스팅, 다수)
        │     └── lsass.exe (인증/보안)
        ├── csrss.exe (세션1)
        └── winlogon.exe (세션1 로그온)
              └── userinit.exe
                    └── explorer.exe (부모 종료→고아, 정상)
```

## 3. 암기 팁
- **"스크린 → 크사 → 위니 → 서비스/엘새스"**: **S**mss → **C**srss/**W**ininit → **S**ervices/**L**sass 순서로 세션0이 뜬다
- **lsass = "로그인 인증 + 자격증명"** → Mimikatz 표적, 메모리 덤프 공격 대상
- **svchost는 부모가 반드시 services.exe** — 아니면 악성코드 의심(탐지 문제 단골)
- **explorer.exe는 부모(userinit.exe)가 종료돼서 없는 게 정상** — "부모 없음=무조건 악성"이 아님

## 4. 헷갈리는 포인트 (기출 트랩)

| 함정 문장 | 정답 |
|---|---|
| csrss.exe는 항상 부모가 떠 있어야 정상? | ❌ 실행 후 부모 종료돼도 정상 |
| svchost.exe 부모가 explorer.exe여도 정상? | ❌ 반드시 services.exe여야 정상, 아니면 위장 의심 |
| lsass.exe는 세션1(사용자)에서 실행? | ❌ 세션0(wininit.exe 자식)에서 실행 |
| svchost.exe가 하나만 있어야 정상? | ❌ 여러 개 존재하는 게 정상(서비스 그룹별로 다수) |
| explorer.exe 부모 프로세스 없으면 악성코드? | ❌ userinit.exe가 정상 종료된 결과라 부모 없어도 정상 |

## 관련 기출/문제
- 프로세스별 정상 부모 매칭 문제
- 위장 악성코드 탐지 시나리오(부모 프로세스 이상 여부)
- PID 4(System)의 의미

## 💬 내 코멘트
- 
