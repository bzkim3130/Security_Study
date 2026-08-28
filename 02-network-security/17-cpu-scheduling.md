# 선점(Preemptive) vs 비선점(Non-preemptive) CPU 스케줄링

## 한줄 요약
> 비선점은 "CPU를 프로세스가 스스로 반납할 때까지 기다림", 선점은 "우선순위 높은 게 오면 강제로 뺏음" — 응답성과 오버헤드가 트레이드오프 관계다.

## 1. 핵심 개념 비교

| 구분 | 비선점 (Non-preemptive) | 선점 (Preemptive) |
|---|---|---|
| CPU 반납 조건 | 프로세스가 스스로 종료/대기할 때만 | 우선순위 높은 프로세스 도착 시 강제 회수 가능 |
| Context Switch 오버헤드 | 낮음 | 높음(빈번한 전환) |
| 응답성 | 낮음(긴 작업이 CPU 독점 가능) | 높음(빠른 응답) |
| Convoy Effect(호위효과) | 발생 가능 (긴 작업 뒤에 짧은 작업들이 줄줄이 대기) | 상대적으로 적음 |
| 구현 복잡도 | 단순 | 복잡 |

## 2. 대표 알고리즘

```
비선점 계열
├── FCFS (First Come First Served) — 도착 순서대로
├── SJF (Shortest Job First) — 실행시간 짧은 것 먼저
├── HRN (Highest Response Ratio Next) — 대기시간 고려한 응답률 우선
└── Priority(비선점형) — 우선순위 순, 실행 중엔 안 뺏김

선점 계열
├── Round Robin (RR) — 시간할당량(Time Quantum)씩 돌아가며 실행
├── SRTF (Shortest Remaining Time First) — SJF의 선점형 버전
├── Multilevel Queue — 큐를 여러 단계로 나눠 우선순위별 처리
└── Multilevel Feedback Queue — 큐 간 이동 가능(적응형)
```

## 3. 분류 다이어그램

```
CPU 스케줄링
├── 비선점 (FCFS, SJF, HRN, Priority)
└── 선점 (RR, SRTF, Multilevel Queue, Multilevel Feedback Queue)
```

## 4. SJF vs SRTF

- **SJF**: 비선점, 한번 시작하면 끝까지 실행 (실행시간 짧은 걸 먼저 고름)
- **SRTF**: SJF의 선점형 버전, 새 프로세스가 왔을 때 "남은 시간"이 더 짧으면 즉시 교체

## 5. Multilevel Queue vs Multilevel Feedback Queue

- **Multilevel Queue**: 큐가 고정 (한번 배정된 큐에서 다른 큐로 이동 불가)
- **Multilevel Feedback Queue**: 프로세스가 큐 간 이동 가능 (오래 기다린 프로세스를 상위 큐로 승격 등, Starvation 방지)

## 암기 팁
- **"비선점=양보할 때까지 못 뺏음, 선점=새치기 당함"**
- **오버헤드 트레이드오프**: "선점은 응답 빠르지만 Context Switch 잦아서 부담, 비선점은 반대"
- **SJF→SRTF**: "짧은 거 먼저(비선점) → 짧은 거 먼저인데 중간에도 바꿈(선점)"
- **Multilevel Feedback = "승진 가능한 큐"** (Multilevel Queue는 "고정 신분")

## 헷갈리는 포인트
- FCFS는 선점이 절대 아님 — 순서대로 끝까지 실행
- Convoy Effect는 **비선점 계열(특히 FCFS)**에서 두드러지는 현상 — 긴 작업 뒤에 짧은 작업들이 줄줄이 밀림

## 관련 기출/문제
- 선점/비선점 알고리즘 분류 매칭
- SJF vs SRTF 차이
- Convoy Effect가 발생하는 조건

## 💬 내 코멘트
- 
