# 디스크 / 파일시스템 분석 - Windows 아티팩트 (Spool file, 섬네일 캐시)

## 한줄 요약
> 스풀 파일은 "누가 언제 뭘 인쇄했는지", 섬네일 캐시는 "삭제된 파일의 흔적"을 보여주는 대표적인 Windows 포렌식 아티팩트다.

## 1. Print Spool 파일 포렌식

**저장 위치**: `C:\Windows\System32\spool\PRINTERS\`

인쇄 작업 하나당 파일 2종류 생성:

| 파일 | 역할 | 담긴 정보 |
|---|---|---|
| SHD (Shadow) | 메타데이터 | 문서명, 사용자 계정, 프린터명, 인쇄 시각, 요청한 컴퓨터명 |
| SPL | 실제 인쇄 데이터 | EMF 또는 RAW 형식 |

**SPL 데이터 형식 2종류**
- **EMF (Enhanced Metafile)**: 원본 문서 내용을 복원 가능 → 포렌식 가치 높음
- **RAW**: 프린터 전용 형식 (PCL, PostScript 등) — 복원이 상대적으로 어려움

## 암기 팁 (Spool)
- **"SHD는 신원, SPL은 실물"** — SHD는 누가/언제(메타데이터), SPL은 실제 인쇄된 내용물

## 2. 섬네일 캐시(Thumbnail Cache) 포렌식

**저장 위치** (Windows Vista 이후): `%userprofile%\AppData\Local\Microsoft\Windows\Explorer`

**파일 종류**
- `thumbcache_32.db`, `thumbcache_96.db`, `thumbcache_256.db`, `thumbcache_1024.db` 등 → 숫자는 썸네일 크기(px), **확장자는 전부 .db로 동일**
- `thumbcache_idx.db`: 인덱스 파일
- `thumbcache_sr.db`: 시스템 리소스 관련 파일

**크기별 내부 인코딩 형식**

| 파일 | 내부 이미지 형식 |
|---|---|
| thumbcache_32.db | BMP |
| thumbcache_96.db | BMP |
| thumbcache_256.db | JPEG 또는 PNG |
| thumbcache_1024.db | JPEG |

**포렌식 가치**: 원본 파일이 삭제돼도 썸네일 캐시에는 이미지 흔적이 남아있을 수 있어 **삭제된 파일 존재 증명**에 활용됨

## 암기 팁 (섬네일 캐시)
- 작은 크기(32/96) = **BMP**, 큰 크기(256/1024) = **JPEG 계열** → "작으면 단순포맷(BMP), 크면 압축포맷(JPEG)"
- **"GIF는 없다"**: thumbcache 어느 크기에도 GIF 포맷은 사용되지 않음 → 시험에서 GIF 나오면 바로 오답 소거

## 헷갈리는 포인트
- 파일명 숫자만 다르고 확장자는 모두 `.db`로 동일 (thumbcache_32.db, thumbcache_1024.db 등)
- 256px 파일은 JPEG **또는** PNG 둘 다 가능 (상황에 따라 다름) — 단정적으로 하나만 고르면 안 됨

## 관련 기출/문제
- SHD/SPL 각각에 담긴 정보 구분 문제
- 썸네일 캐시 크기별 내부 인코딩 형식 매칭 문제 (GIF는 함정 오답으로 자주 등장)

## 💬 내 코멘트
- 
