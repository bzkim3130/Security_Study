# 데이터베이스 명령어 분류 (DDL / DML / DCL / TCL)

## 한줄 요약
> DB 명령어는 "무엇을 대상으로 하느냐"에 따라 구조(DDL)/데이터(DML)/권한(DCL)/트랜잭션(TCL) 4가지로 나뉘며, DDL은 자동 커밋(롤백 불가)이라는 게 핵심 함정이다.

## 1. 핵심 분류표

| 구분 | 풀네임 | 대상 | 주요 명령어 | 특징 |
|---|---|---|---|---|
| DDL | Data Definition Language | 테이블/스키마 구조 | CREATE, ALTER, DROP, TRUNCATE | 실행 즉시 자동 커밋 |
| DML | Data Manipulation Language | 데이터(행) | SELECT, INSERT, UPDATE, DELETE | 트랜잭션 대상(롤백 가능) |
| DCL | Data Control Language | 권한 | GRANT, REVOKE | 사용자 접근 통제 |
| TCL | Transaction Control Language | 트랜잭션 | COMMIT, ROLLBACK, SAVEPOINT | DML 결과 확정/취소 |

## 2. 도식화

```
SQL 명령어
├── DDL(구조): CREATE(생성) / ALTER(변경) / DROP(삭제-구조) / TRUNCATE(초기화)
├── DML(데이터): SELECT(조회) / INSERT(삽입) / UPDATE(수정) / DELETE(삭제-데이터)
├── DCL(권한): GRANT(부여) / REVOKE(회수)
└── TCL(트랜잭션): COMMIT(확정) / ROLLBACK(취소) / SAVEPOINT(지점저장)
```

## 3. DROP vs TRUNCATE vs DELETE (시험 단골)

| 명령 | 분류 | 대상 | 롤백 가능? | 속도 |
|---|---|---|---|---|
| DROP | DDL | 테이블 구조 자체 삭제 | 불가 | - |
| TRUNCATE | DDL | 데이터만 전체 삭제, 구조는 유지 | 불가 | 매우 빠름(페이지 단위) |
| DELETE | DML | 조건별 행 삭제 | 가능 | 상대적으로 느림(로그 남김) |

## 암기 팁
- **"디비권트"**: **D**DL(구조) - **D**ML(데이터) - **C**DL(권한) - **T**CL(트랜잭션)
- DDL = **D**efinition = "**집**을 짓다/허물다"(구조) → CREATE/ALTER/DROP
- DCL = **C**ontrol = "**문**을 열고 닫다"(권한) → GRANT(열기)/REVOKE(닫기)
- DML = **CRUD**와 대응: Create=INSERT, Read=SELECT, Update=UPDATE, Delete=DELETE
- **"DDL/DCL은 자동커밋이라 되돌릴 수 없다"**

## 헷갈리는 포인트
- **TRUNCATE를 DML로 착각**하는 함정 자주 출제 — TRUNCATE는 데이터만 지워도 **DDL**(구조 관련 명령으로 분류, 자동커밋)
- 일부 교재는 SELECT만 떼서 **DQL(Data Query Language)**로 별도 분류 — 문제에서 DQL이 보기에 있으면 SELECT를 그쪽으로 답해야 할 수 있음
- GRANT/REVOKE는 테이블/뷰/특정 컬럼 단위까지도 권한 부여 가능

## 관련 기출/문제
- "다음 중 DDL에 해당하지 않는 것은?" → TRUNCATE를 DML로 착각하는 함정
- DROP/TRUNCATE/DELETE 롤백 가능여부 비교
- GRANT/REVOKE의 분류(DCL) 확인

## 💬 내 코멘트
- 
