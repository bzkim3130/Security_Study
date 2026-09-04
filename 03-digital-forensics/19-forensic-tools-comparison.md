# 디지털포렌식 도구 비교 및 운영체제 지원

## 한줄 요약
> 포렌식 도구는 상용(EnCase/FTK/BlackLight, 대부분 Windows전용)과 오픈소스(Autopsy/TSK/TCT, 대부분 크로스플랫폼)로 나뉘며, TCT만 유일하게 Unix/Linux 전용이라는 점이 시험 단골 함정이다.

## 1. 주요 포렌식 도구 정리

| 도구 | 유형 | 지원 OS | 특징 |
|---|---|---|---|
| EnCase | 상용(OpenText/Guidance) | **Windows only**(분석기) | 법정 증거능력 표준, .E01 이미지 포맷 원조 |
| FTK (Forensic Toolkit) | 상용(Exterro) | **Windows only** | 인덱싱 기반 고속검색, 이메일/DB 분석 강점 |
| FTK Imager | 무료(Exterro 배포) | Windows | 이미징 전용, **분석 기능 없음** |
| X-Ways Forensics | 상용 | Windows | 경량·고속, 독일산, 헥스 분석 강점 |
| BlackLight | 상용(Cellebrite/BlackBag) | **Windows + macOS** | 원래 Mac 포렌식 특화(BlackBag Technologies 제품) |
| Cellebrite UFED | 상용 | Windows | 모바일 포렌식 대표 툴 |
| Magnet AXIOM | 상용 | Windows | 디스크+모바일+클라우드 통합 분석 |
| Autopsy | 무료·오픈소스 | **Win/Linux/macOS**(Java기반) | Sleuth Kit의 GUI 프론트엔드 |
| The Sleuth Kit (TSK) | 무료·오픈소스 | **Win/Linux/Unix/macOS** | 커맨드라인 파일시스템 분석 엔진 |
| TCT (Coroner's Toolkit) | 무료·오픈소스 | **Unix/Linux 계열만**(Windows 미지원) | Dan Farmer·Wietse Venema 제작, TSK의 전신 |
| Volatility | 무료·오픈소스 | Win/Linux/Mac(Python) | **메모리(RAM) 포렌식** 전용 |
| CAINE | 무료(부팅용 배포판) | Linux 기반 | 라이브 포렌식 부팅 CD |
| SIFT Workstation | 무료(SANS) | Linux(Ubuntu 기반) | 종합 포렌식 배포판 |
| Redline | 무료(Mandiant) | Windows | 메모리/엔드포인트 침해 분석 |

## 2. 도구 계보

```
TCT (1999, Dan Farmer·Wietse Venema)
   │  Unix 전용
   ▼
(Brian Carrier가 확장) → TASK
   ▼
The Sleuth Kit (TSK) — CLI 엔진, Win/Linux/Unix/Mac 지원
   │
   ▼ (GUI 입힘)
Autopsy — TSK 기반 GUI 프론트엔드, 무료·오픈소스

[별개 계보 - 상용]
EnCase / FTK / X-Ways → 전부 Windows 전용
BlackLight → 원래 Mac 전문 → Windows도 지원(예외적으로 크로스플랫폼)
```

## 3. 유형별 분류

```
디지털포렌식 도구
├── 상용 (Windows 전용 대부분)
│    EnCase, FTK, X-Ways, Cellebrite UFED, Magnet AXIOM
│    └── 예외: BlackLight (Windows+macOS)
│
└── 오픈소스 (대부분 크로스플랫폼)
     Autopsy, TSK (Win/Linux/Unix/Mac)
     └── 예외: TCT (Unix/Linux 전용, Windows 미지원)
```

## 암기 팁
- **"TCT는 유닉스에 갇혀있다"** — TCT만 Unix 전용, 나머지 오픈소스(TSK/Autopsy)는 크로스플랫폼
- **"상용툴 = 윈도우 감옥"** — EnCase/FTK/X-Ways는 Windows에서만 동작, BlackLight만 예외(Mac도 지원)
- **"오리진 스토리"**: TCT(1999) → TASK → **Sleuth Kit(TSK)** → GUI 입힌 게 **Autopsy** (Autopsy는 TSK의 껍데기)

## 헷갈리는 포인트
- "Autopsy는 상용 도구다?" → ❌ 무료·오픈소스
- "TSK는 GUI 툴이다?" → ❌ CLI(커맨드라인) 엔진, GUI는 Autopsy가 담당
- "FTK Imager로 파일시스템 분석 가능?" → ❌ 이미징 전용, 분석은 FTK 본체에서
- "EnCase도 Mac에서 돌아간다?" → ❌ 분석기는 Windows 전용
- "BlackLight는 원래 모바일 전문 툴?" → ❌ 원래 Mac(macOS) 전문, 이후 Cellebrite가 인수

## 관련 기출/문제
- "다음 중 유닉스 계열에서만 동작하는 포렌식 도구는?" → **TCT** (단골 문제)
- "메모리 포렌식 전용 도구는?" → **Volatility**
- "Sleuth Kit과 Autopsy의 관계는?" → Autopsy = TSK 기반 GUI 프론트엔드
- 상용 vs 오픈소스 구분 객관식(EnCase/FTK를 오픈소스로 헷갈리게 하는 보기 자주 출제)

## 💬 내 코멘트
- 
