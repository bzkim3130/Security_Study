# USBSTOR 서브키 분석

## 한줄 요약
> USBSTOR는 연결됐던 USB 저장장치의 흔적(장치정보/시리얼번호/시각)을 남기지만 "누가 썼는지"는 알려주지 않으며, 이를 알려면 MountPoints2(사용자별, HKCU)까지 함께 봐야 한다.

## 1. 핵심 개념

| 항목 | 내용 |
|---|---|
| 레지스트리 경로 | `HKLM\SYSTEM\CurrentControlSet\Enum\USBSTOR` |
| 저장 정보 | 연결됐던 USB **저장장치**의 벤더/제품/리비전, 시리얼번호 |
| 하위 구조 | ① 장치 클래스 키 → ② 인스턴스 ID(시리얼번호) 키 |
| FriendlyName | 장치 표시 이름(예: "SanDisk Cruzer USB Device") |
| ParentIdPrefix | 시리얼번호가 없는 장치일 때 사용되는 대체 식별값 |
| LastWrite Time | **인스턴스 ID 키**의 LastWrite = 해당 장치의 **최종 연결 시각**(근사치) |
| 함께 봐야 할 키 | `Enum\USB`(VID/PID만), `MountedDevices`, `MountPoints2`(사용자별), `SetupAPI.dev.log`(최초 설치시각) |

## 2. 레지스트리 계층 구조

```
USBSTOR 루트키 (Enum\USBSTOR)
  └── 장치 클래스 키 (Disk&Ven_X&Prod_Y&Rev_Z)
        └── 인스턴스 ID (시리얼번호) 키
              ├── FriendlyName (표시이름)
              ├── ParentIdPrefix (시리얼없을때 대체값)
              └── LastWrite Time (최종연결시각 근사)
```

## 3. "무엇-어디로-누가-언제" 4단계 사용이력 재구성

```
무엇 → USBSTOR (Class ID + Instance ID)
어디로 → MountedDevices (드라이브 문자)
누가 → MountPoints2 (사용자 계정, NTUSER.DAT)
언제 → LastWrite / SetupAPI.dev.log (시각)

무엇 + 어디로 + 누가 + 언제 = 완전한 사용 이력 재구성
```

## 4. 핵심 4개 키 역할 구분

| 키 | 위치 | 알려주는 것 |
|---|---|---|
| USBSTOR | HKLM(시스템 전역) | 장치 자체 정보(모델/시리얼) |
| MountedDevices | HKLM | 드라이브 문자 매핑(어디로 마운트됐는지) |
| MountPoints2 | **HKCU**(NTUSER.DAT, 사용자별) | **누가** 그 장치를 사용했는지 |
| SetupAPI.dev.log | 로그 파일 | 최초 설치 시각 |

## 암기 팁
- **"무-어-누-언" 4단계 스토리**: 무엇(USBSTOR) → 어디로(MountedDevices) → 누가(MountPoints2) → 언제(LastWrite/SetupAPI)
- Instance ID 끝자리에 **"&0"이 붙어있으면 시리얼번호가 아니라 Windows가 만든 가짜값**

## 헷갈리는 포인트
- **Device Class ID vs Instance ID**: 둘 다 같은 걸 가리킨다고 착각하기 쉬움 — Class ID는 **모델**, Instance ID는 **개체 하나하나**(같은 모델 USB 2개 꽂으면 Class ID는 같고 Instance ID는 다름)
- **MountedDevices 값 매핑**: 값 이름(`\DosDevices\E:`)이 장치 자체 정보라고 착각 — 값 이름은 **드라이브 문자**, 값 데이터(바이너리)가 **장치 시그니처**(볼륨 시리얼 포함)
- **MountPoints2가 시스템 전역 정보라는 착각**: HKLM에 있을 거라 생각하기 쉬움 — 실제로는 **HKCU(NTUSER.DAT) 하위**, 사용자 계정별로 별도 존재
- **ParentIdPrefix = FriendlyName**: 둘 다 이름 관련 값이라 혼동 — FriendlyName은 **표시용 이름**, ParentIdPrefix는 **식별자 대체값**(전혀 다른 역할)

## 관련 기출/문제
- "특정 사용자 계정이 USB를 사용한 흔적"을 물으면 → **MountPoints2**가 정답(USBSTOR만으론 사용자 특정 불가)
- Instance ID 끝의 "&0" 존재 여부로 진짜 시리얼번호 유무 판별 문제
- 드라이브 문자와 실제 물리 장치를 매칭시키는 키 → **MountedDevices**(USBSTOR와 혼동 유도하는 보기 자주 등장)
- MTP 방식(스마트폰/카메라) 연결은 USBSTOR가 아니라 **WPDBUSENUM** 확인 문제

## 💬 내 코멘트
- 
