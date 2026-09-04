# IE10/11 WebCacheV24.dat 브라우저 아티팩트

## 한줄 요약
> IE10부터는 Cache/History/Cookie/Download List를 index.dat 대신 WebCacheV24.dat 하나의 ESE DB 파일로 통합 관리하며, TaskHost 프로세스가 이 파일을 점유해서 일반 복사로는 수집할 수 없다.

## 1. 핵심 개념

| 구분 | 내용 |
|---|---|
| 저장 파일 | WebCacheV24.dat |
| 저장 방식 | Cache, History, Cookie, Download List 인덱스 정보를 **하나의 파일로 통합** |
| 저장 경로 | `\Users\%Username%\AppData\Local\Microsoft\Windows\WebCache\WebCacheV24.dat` |
| 파일 포맷 | Microsoft **ISAM**(Indexed Sequential Access Method) 방식의 **ESE(Extensible Storage Engine) DB** |
| 잠금 프로세스 | **TaskHost** 프로세스가 파일을 점유 → 일반 복사(Ctrl+C)로 수집 불가, FTK Imager 등 라이브 수집 도구 필요 |
| 이전 버전(IE9 이하) | index.dat 파일 존재(Cache/History/Cookie 등 **개별** 관리) |

## 2. 버전별 구조 변화

```
IE9 이하
  └── index.dat 개별 존재 (Cache/History/Cookie 등 각각 별도 파일)

IE10 이상
  └── WebCacheV24.dat 단일 파일 (ESE DB, ISAM 방식)
        └── TaskHost 프로세스가 점유중
              └── ⚠️ 일반 복사 불가, 라이브 수집 도구 필요
```

## 3. 함정: "버전 기준"은 Windows가 아니라 IE

**핵심 함정**: "Windows 8/10에는 index.dat가 존재하지 않는다"는 표현이 오답으로 자주 등장 — 정확히는 **IE10 버전 이상**부터 통합되는 것이지, **Windows OS 버전**이 기준이 아님 (같은 Windows에서도 오래된 IE 버전이면 index.dat 방식일 수 있음)

## 암기 팁
- **"IE10부터는 WebCache, 하나로 다 캐(cache)"** — Cache/History/Cookie/Download가 WebCacheV24.dat 하나로 합쳐짐
- **"Task가 잡고 있어서 못 훔쳐가(Host)"** — TaskHost 프로세스 점유 = 일반 복사 불가
- 경로 암기: **AppData\Local\Microsoft\Windows\WebCache** (index.dat 시절 경로에서 `\WebCache` 폴더만 추가된 것)

## 헷갈리는 포인트
- index.dat vs WebCacheV24.dat: 저장 경로가 유사해서 헷갈림(뒤에 `\WebCache\` 유무 차이)
- "일반적인 파일 복사로 수집 가능하다" → **오답**(TaskHost 점유로 불가)
- 버전 기준이 **Windows 버전이 아니라 IE 버전**이라는 점을 놓치기 쉬움

## 관련 기출/문제
- "Windows 8/10에는 index.dat가 존재하지 않는다" 식의 서술 → 버전 조건이 틀렸는지 확인(핵심은 IE10 이상 여부)
- ISAM/ESE 포맷 명칭 암기(Microsoft 개발, Extensible Storage Engine)
- TaskHost 프로세스로 인한 수집 제약(라이브 수집 필요) → 실무형 지문으로 자주 출제

## 💬 내 코멘트
- 
