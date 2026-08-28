# PAM (Pluggable Authentication Modules)

## 한줄 요약
> PAM은 Linux/Unix에서 인증 절차를 애플리케이션과 분리해 모듈화한 프레임워크로, auth/account/password/session 4개 관리 그룹과 control flag로 동작을 조립한다.

## 1. 개요

PAM은 로그인, su, sudo, sshd, passwd 등 대부분의 인증 관련 프로그램이 사용하는 **인증 프레임워크**. 애플리케이션이 인증 로직을 직접 구현하지 않고, 필요한 인증 모듈을 "플러그인"처럼 조립해서 씀.

## 2. PAM의 4가지 관리 그룹

| 그룹 | 역할 |
|---|---|
| auth | 사용자가 누구인지 인증 (패스워드 확인, OTP 검증 등) |
| account | 계정 자체의 유효성 확인 (계정 만료, 접속 시간 제한 등) |
| password | 인증 정보(패스워드 등) 갱신 처리 |
| session | 인증 성공 전후 세션 설정/해제 (로그 기록, 홈 디렉토리 마운트 등) |

## 3. 설정 파일 구조

**위치**: `/etc/pam.d/<서비스명>` (예: `/etc/pam.d/sshd`)

```
<type>  <control>  <module-path>  [module-arguments]

예: auth       required     pam_unix.so
    auth       requisite    pam_deny.so
    account    required     pam_unix.so
    session    required     pam_limits.so
```

## 4. Control Flag (제어 플래그)

| 플래그 | 동작 |
|---|---|
| required | 실패해도 즉시 종료 안 함, 나머지 모듈 다 실행 후 최종 실패 처리 |
| requisite | 실패 시 즉시 인증 중단 |
| sufficient | 성공하면 즉시 인증 성공 처리 (이전 required 실패 없어야 함) |
| optional | 성공/실패가 전체 결과에 영향 없음 |
| include | 다른 설정 파일의 규칙 포함 |

## 5. 대표 PAM 모듈

| 모듈 | 기능 |
|---|---|
| pam_unix.so | 표준 Unix 패스워드(/etc/passwd, /etc/shadow) 인증 |
| pam_deny.so | 무조건 실패 반환 |
| pam_permit.so | 무조건 성공 반환 |
| pam_limits.so | 사용자별 리소스 제한 적용 |
| pam_tally2.so / pam_faillock.so | 로그인 실패 횟수 추적 → 계정 잠금 |
| pam_google_authenticator.so | OTP(2FA) 인증 |
| pam_ldap.so / pam_sss.so | LDAP/SSSD 연동 중앙 인증 |
| pam_wheel.so | wheel 그룹만 su 허용 |
| **pam_pwquality.so** | **패스워드 복잡도(품질) 정책 검사** |

## 5-1. pam_pwquality.so & pwquality.conf 상세

**설정 파일**: `/etc/security/pwquality.conf`

| 지시어 | 의미 |
|---|---|
| minlen | 최소 길이 |
| minclass | 최소 문자 종류 수 (대문자/소문자/숫자/특수문자 중 몇 종류 이상) |
| dcredit | 숫자(digit) 관련 |
| ucredit | 대문자(upper) 관련 |
| lcredit | 소문자(lower) 관련 |
| ocredit | 특수문자(other) 관련 |
| maxrepeat | 동일 문자 연속 최대 허용 횟수 |
| maxsequence | 연속된 문자열(abc, 123 등) 최대 허용 길이 |
| difok | 이전 패스워드와 달라야 하는 최소 문자 수 |
| retry | 재시도 허용 횟수 |
| enforce_for_root | root 계정에도 정책 적용 여부 |
| dictcheck | 사전 단어 포함 여부 검사 |

**credit 값의 부호 규칙 (핵심 함정)**
```
음수(-N) = "최소 N개 이상 반드시 포함해야 함" (강제)
양수(+N) = "포함하면 길이 보너스로 N만큼 인정" (권장, 강제 아님)

예: dcredit = -1  → 숫자를 최소 1개는 반드시 포함해야 함
    dcredit = 1   → 숫자를 포함하면 길이 요건에 보너스로 카운트(강제 아님)
```

**PAM 연동 필수 조건**: pwquality.conf 설정만으로는 적용 안 됨 — **`/etc/pam.d/system-auth` 또는 `/etc/pam.d/password-auth`에 `pam_pwquality.so`가 등록**되어 있어야 실제로 정책이 작동함

## 5-2. pwquality.conf vs login.defs 구분

| 구분 | pwquality.conf | login.defs |
|---|---|---|
| 성격 | 패스워드 **복잡도(품질)** 정책 | 패스워드 **기간/에이징(aging)** 정책 |
| 예시 항목 | minlen, dcredit, maxrepeat | PASS_MAX_DAYS, PASS_MIN_DAYS, PASS_WARN_AGE |
| 적용 시점 | 패스워드 **설정할 때** 복잡도 검사 | 패스워드 **유효기간/만료** 관리 |

## 6. 보안/포렌식 관점 점검 포인트

- PAM은 인증의 핵심 관문 → 백도어 삽입 시 자주 노려지는 지점 (악성 pam_unix.so 교체 등)
- `/etc/pam.d/` 내 파일들의 최근 변경 시각(mtime) 확인
- 주요 `.so` 파일 해시값 검증 (변조 여부)
- `sufficient` 플래그가 예상치 못한 위치에 삽입되어 인증 우회를 허용하는지 확인
- SSH 침해사고 시 `/etc/pam.d/sshd`와 `sshd_config`를 함께 대조
- 로그 위치: `/var/log/auth.log`(Debian) 또는 `/var/log/secure`(RHEL)

## 암기 팁
- **4개 관리그룹**: "인증(auth) → 계정확인(account) → 비번갱신(password) → 세션처리(session)"
- **Control Flag 강도순**: "requisite(즉시중단) > required(끝까지 검사 후 실패) > sufficient(성공하면 바로 통과) > optional(있으나마나)"
- **백도어 점검 = "sufficient가 이상한 자리에 몰래 껴있는지 확인"**
- **DULO 니모닉**: dcredit-ucredit-lcredit-ocredit = **D**igit-**U**pper-**L**ower-**O**ther 순서
- **credit 부호**: "마이너스(-)는 강제, 플러스(+)는 보너스" — 부호 하나로 의미가 정반대

## 헷갈리는 포인트
- **required vs requisite**: 둘 다 "실패하면 최종 거부"지만, required는 나머지 모듈도 다 돌려보고 실패 처리, requisite는 그 자리에서 즉시 중단
- PAM은 "인증 자체를 수행"하는 게 아니라, 인증 모듈들을 **조립·중계하는 프레임워크**
- **pwquality.conf(복잡도) vs login.defs(기간/에이징)를 혼동하기 쉬움** — 하나는 "패스워드가 얼마나 복잡한지", 하나는 "패스워드를 얼마나 오래 쓸 수 있는지"
- pwquality.conf 설정만 해두고 **pam_pwquality.so를 pam.d에 등록 안 하면 아무 효과 없음** — 설정파일과 PAM 연동은 별개 단계

## 관련 기출/문제
- Control Flag 4종류 동작 차이 구분
- PAM 4개 관리그룹 역할 매칭
- PAM 설정 조작을 이용한 백도어 탐지 포인트
- dcredit/ucredit/lcredit/ocredit의 부호(+/-) 의미 차이
- pwquality.conf vs login.defs 역할 구분

## 💬 내 코멘트
- 
