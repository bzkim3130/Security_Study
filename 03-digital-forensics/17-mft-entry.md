# MFT Entry 구조 및 포렌식 분석

## 한줄 요약
> MFT Entry는 NTFS에서 파일/디렉터리 하나당 1개씩 할당되는 1024바이트 고정 레코드이며, $STANDARD_INFORMATION(조작 쉬움)과 $FILE_NAME(조작 어려움)의 타임스탬프 불일치가 안티포렌식(Timestomping) 탐지의 핵심이다.

## 1. MFT Entry 기본 정보

| 항목 | 내용 |
|---|---|
| 위치 | $MFT (볼륨 최상위의 메타파일) |
| 기본 크기 | 1개 Entry = **1024바이트**(기본값, 볼륨 생성시 결정) |
| 대상 | 모든 파일, 디렉터리, $MFT 자기 자신도 Entry를 가짐(Entry #0) |
| 구조 | Entry Header + 여러 개의 Attribute(속성) |
| 정상 시그니처 | Entry 시작 4바이트 = **"FILE"**(0x46 49 4C 45) |
| 손상 시그니처 | "FILE" 대신 **"BAAD"**로 바뀜 |

## 2. MFT Entry 내부 구조

| 구성요소 | 설명 |
|---|---|
| Entry Header | 시그니처(FILE/BAAD), Sequence Number, 링크카운트, 첫속성오프셋, Flag(할당/삭제, 파일/디렉터리) |
| $STANDARD_INFORMATION (0x10) | MACE 타임스탬프(수정/접근/생성/MFT수정), 소유자, 권한플래그 — **일반 도구로 조작 쉬움** |
| $FILE_NAME (0x30) | 파일명, 부모디렉터리 참조, MACE 타임스탬프(중복저장) — **시스템레벨이라 조작 어려움** |
| $DATA (0x80) | 실제 파일데이터(상주) 또는 데이터 위치정보(비상주) |
| $INDEX_ROOT/$INDEX_ALLOCATION | 디렉터리인 경우 하위항목 인덱스 |

## 3. 구조 도식화

```
$MFT (Master File Table)
  └── MFT Entry #N (1024바이트)
        ├── Entry Header (시그니처FILE/BAAD, Sequence#, Flag)
        ├── $STANDARD_INFORMATION(0x10) — MACE 타임스탬프 (⚠조작쉬움)
        ├── $FILE_NAME(0x30) — 파일명+MACE 타임스탬프 (⚠조작어려움)
        ├── $DATA(0x80)
        │     ├── Resident(작은파일, ~700바이트 이하) → Entry 안에 직접 저장
        │     └── Non-Resident(큰파일) → Data Run으로 클러스터 위치만 기록
        └── $INDEX_ROOT/$INDEX_ALLOCATION (디렉터리인 경우)
```

## 4. $STANDARD_INFORMATION vs $FILE_NAME (핵심 함정)

| 구분 | $STANDARD_INFORMATION | $FILE_NAME |
|---|---|---|
| 조작 난이도 | 일반 툴(explorer 등)로 쉽게 변경 가능 | 시스템 API 레벨이라 변경 어려움 |
| 포렌식 활용 | 타임스탬프 위조(안티포렌식) 탐지 시 **비교 기준** | 위조 탐지 시 **신뢰 기준선(baseline)** |
| Timestomping 탐지 | $SI ≠ $FN 인 경우 → **타임스탬프 조작 의심 정황** |

## 5. Resident vs Non-Resident

파일이 작으면(대략 700바이트 이하, 클러스터 크기 따라 다름) MFT Entry 안에 데이터가 **직접** 들어감(Resident) → 파일 삭제돼도 MFT Entry만 남아있으면 데이터 복구 가능한 경우 있음

## 암기 팁
- **"FILE이 살아있고, BAAD가 죽었다"** — 정상 Entry는 "FILE", 손상되면 "BAAD"
- **"$SI는 쉽고, $FN은 어렵다"** — $S**I**(Standard Info)는 Simple(쉬움), $F**N**(File Name)은 Firm(단단함)
- **"1024 = 1KB, 볼륨 하나당 크기 고정"**

## 헷갈리는 포인트
- $STANDARD_INFORMATION과 $FILE_NAME 둘 다 MACE 타임스탬프를 갖고 있다는 걸 놓치기 쉬움 — 중복 저장되기 때문에 **둘을 대조**해서 조작 여부 판단 가능
- "FILE" 시그니처가 항상 정상이라고 착각 — 손상되면 "BAAD"로 바뀜, 시험에서 헷갈리게 출제됨
- Resident 파일은 삭제돼도 **MFT Entry만으로 복구 가능**할 수 있다는 점 — Non-Resident는 별도 클러스터까지 확인해야 함

## 관련 기출/문제
- "FILE" 시그니처와 "BAAD"(손상) 구분 문제
- $STANDARD_INFORMATION vs $FILE_NAME 타임스탬프 불일치 → 안티포렌식 탐지 근거로 자주 출제
- MFT Entry 크기(1024바이트) 수치 암기
- Resident/Non-Resident 데이터 저장 방식 구분

## 💬 내 코멘트
- 
