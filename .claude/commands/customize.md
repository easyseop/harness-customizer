---
description: 하네스 템플릿 커스터마이징 워크플로우. 요구사항 입력 → 파일 탐색 → 수정 제안 → 시뮬레이션 → 판정 → 승인 후 적용.
---

# /customize — 하네스 템플릿 커스터마이징

> 요구사항을 말하면 어느 파일을 어떻게 수정해야 하는지 찾고,
> 시뮬레이션으로 효과를 보여준 뒤 승인 시 적용합니다.

---

## 에이전트 역할 분담

| 에이전트 | 역할 | 담당 단계 |
|---------|------|---------|
| `harness-navigator` | 도메인 확인 + 대상 파일 탐색 | Step 0~1 |
| `harness-customizer` | 수정 내용 제안 + 승인 처리 + 로그 | Step 2, 5 |
| `harness-simulator` | Before/After 시뮬레이션 | Step 3 |
| `harness-judge` | 효과 판정 + 충분성 평가 | Step 4, 6 |

---

## 실행 순서

### Step 0~1: 파일 탐색 (`harness-navigator`)

1. `domain-knowledge/` 확인 — 사내 도메인 키워드 감지 시
   - 관련 파일 있음 → 참고하여 진행
   - 없음 → 개발자에게 추가 요청 또는 베스트 에포트 선택지 제시
2. `HARNESS-MAP.md` 참조 → 대상 파일 식별
3. 출력: 파일 경로 + 관련 섹션 + 영역 코드

### Step 2: 수정 내용 제안 (`harness-customizer`)

1. 탐색된 파일 Read
2. 현재 스타일에 맞는 추가 내용 생성
3. 출력: 수정 대상 + 삽입 위치 + 추가 내용 + 이유

### Step 3: 시뮬레이션 (`harness-simulator`)

1. 테스트 시나리오 생성 (파일 타입에 맞게)
2. Before 시뮬레이션 실행
3. After 시뮬레이션 실행 (수정 내용 반영, 파일 미수정)

### Step 4: 효과 판정 (`harness-judge`)

수정 파일 종류에 따라 판정 방식이 다르다.

**gate `.sh` 수정 시 (Phase 1 — 실제 실행)**
1. PositiveCase / NegativeCase 임시 파일 생성
2. bash로 gate 스크립트 실제 실행
3. 실제 결과로 Pass/Fail 판정
4. 테스트 파일 정리

**agent `.md` 수정 시 (3단계 검증)**
1. Step A — 커버리지 체크: 추가된 규칙이 테스트에 모두 커버되는지
2. Step B — Cousin Prompt: 시나리오를 3가지 표현으로 변형해 일관성 확인
3. Step C — 페어와이즈 10회: Before vs After 비교, 8/10 이상 After 선택 시 PASS

**그 외 파일 수정 시 (LLM 예측 기반)**
1. Before/After 시뮬레이션 비교
2. 요구사항 달성 여부 Pass/Fail 판정

모든 경우: Fail → Step 2로 돌아가 재제안

### Step 5: 승인 및 적용 (`harness-customizer`)

```
적용하시겠습니까? (yes / 수정 요청 / 취소)
```

- **yes** → 실제 파일 수정 + `customization-log.md` 업데이트
- **수정 요청** → Step 2부터 재실행
- **취소** → 종료

### Step 6: 충분성 평가 (`harness-judge`)

적용 또는 취소 후 자동 실행.
불충분 신호 감지 시 `KNOWLEDGE-GAPS.md`에 기록.

---

## 출력 형식 요약

```
📋 요구사항: [입력 내용]

[Step 0~1] 📁 대상 파일: [경로] / 영역: [A~F]

[Step 2]
📍 삽입 위치: [섹션]
📝 추가 내용: ...

[Step 3]
── Before ─────
[시뮬레이션]
── After ──────
[시뮬레이션]

[Step 4] 📊 효과 판정: Pass / Fail

적용하시겠습니까?
```

---

## 주의

- 실제 파일 수정은 Step 5에서 명시적으로 승인한 경우에만 진행
- 시뮬레이션은 LLM 예측이며 실제 동작과 차이가 있을 수 있음
