# ADB (Android Debug Bridge) 명령어

## 한줄 요약
> ADB는 PC-ADB서버-Android기기 3단 구조로 통신하며, `pull`(파일단위 추출) vs `backup`(앱데이터 전체 백업)의 차이를 아는 게 포렌식 실무의 핵심이다.

## 1. ADB 통신 구조

```
[조사관 PC] ── USB/네트워크 ──▶ [ADB 서버(PC에서 실행)] ──▶ [Android 기기(ADB 데몬)]
```

## 2. 핵심 명령어

| 명령어 | 기능 | 포렌식 맥락 |
|---|---|---|
| `adb devices` | 연결된 기기 목록 확인 | 최초 연결 확인 |
| `adb shell` | 기기 내부 셸 접근 | 명령어 직접 실행 |
| `adb pull` | 기기→PC로 **특정 파일/폴더** 가져오기 | 개별 파일 증거 수집 |
| `adb push` | PC→기기로 파일 전송 | (포렌식에서는 신중히, 원본 훼손 위험) |
| `adb install` | APK 설치 | 분석용 도구 설치 |
| `adb shell pm list packages` | 설치된 패키지(앱) 목록 조회 | 설치 앱 전수조사 |
| `adb logcat` | 시스템 로그 실시간 확인 | 행위 로그 분석 |
| `adb backup` | 앱 데이터 **전체 백업**(tar 형식) | 논리적 추출(단, 최신 Android는 제한 많음) |
| `adb reboot` | 기기 재부팅 | (재부팅 시 휘발성 데이터 손실 위험 — 신중히) |
| `adb forward` | PC-기기 간 포트포워딩 | 원격 디버깅/분석 도구 연동 |

## 3. 명령어 그룹화

```
파일 이동: pull(가져오기) / push(보내기)
정보 조회: devices / shell pm list packages / logcat
백업/설치: backup / install
기타 제어: reboot / forward / shell
```

## 4. pull vs backup 차이 (핵심 함정)

| 구분 | adb pull | adb backup |
|---|---|---|
| 대상 | 지정한 **특정 파일/디렉터리** | **앱 전체 데이터**(설정된 앱들의 데이터 일괄) |
| 형식 | 원본 파일 그대로 | tar 압축 백업 파일(.ab) |
| 루트 권한 | 접근 경로에 따라 필요할 수도 | 앱이 backup 허용 설정했을 때만 가능(allowBackup) |
| 포렌식 활용 | 특정 증거파일 개별 수집 | 앱 데이터 전체 스냅샷 확보 |

## 5. shell vs fastboot 구분

- **adb shell**: 정상 부팅된 OS 상태에서 명령 실행(USB디버깅 활성화 필요)
- **fastboot**: 부트로더 모드에서 동작(OS가 안 올라온 상태), 별도 명령체계

## 6. USB 디버깅 비활성화 시 대안

USB 디버깅이 꺼져있으면 ADB 자체가 안 먹힘 → 이럴 때는:
- **JTAG**: 보드의 테스트 포트를 이용한 저수준 접근
- **ISP (In-System Programming)**: 칩에 직접 프로그래밍 인터페이스로 접근
- **Chip-off**: 저장소 칩 자체를 물리적으로 분리해 읽기

## 암기 팁
- **"디풀푸시 인로백리"**: **디**바이스목록(devices)-**풀**(pull)-**푸시**(push)-**인**스톨(install)-**로**그캣(logcat)-**백**업(backup)-**리**부트(reboot)
- **"pull은 콕 집어서, backup은 통째로"**
- **USB디버깅 꺼짐 → "지제칩" 대안**: **지**터그(JTAG)-**제**이(ISP)-**칩**오프(Chip-off)

## 헷갈리는 포인트
- pull과 backup을 혼동하기 쉬움 — pull은 파일 단위 개별 수집, backup은 앱이 허용한 데이터 전체 일괄 백업
- shell과 fastboot는 **동작 모드 자체가 다름** — shell은 OS 부팅상태, fastboot는 부트로더(OS 이전) 상태
- USB 디버깅이 비활성화된 기기는 ADB로 아무것도 못 함 — 이 경우 무조건 물리적 접근(JTAG/ISP/Chip-off) 검토

## 관련 기출/문제
- ADB 명령어별 기능 매칭
- pull vs backup 차이
- USB 디버깅 비활성화 시 대안 기법(JTAG/ISP/Chip-off)
- shell vs fastboot 모드 차이

## 💬 내 코멘트
- 
