# Harness Template — 빠른 참조 지도

> Source: https://github.com/studioKjm/ai-harness-template
> 에이전트가 요구사항을 받으면 전체 파일을 스캔하기 전에 이 파일을 먼저 읽는다.

---

## 템플릿 폴더 구조

```
ai-harness-template/
├── agents/          ← A. 에이전트 페르소나
├── commands/        ← B. 슬래시 커맨드
├── gates/           ← C. 게이트 스크립트 + 규칙
│   └── rules/       ← D. 선언적 규칙 (yaml)
├── boundaries/      ← E. 훅 + 권한 프리셋
├── methodologies/   ← F. 개발 방법론 모음
├── ouroboros/       ← G. Seed 템플릿 + 모호성 체크
├── methodology/     ← H. 방법론 레지스트리/상태
├── templates/       ← I. 프로젝트 초기화 템플릿 (.hbs)
├── pro/             ← J. Pro 기능 (Python)
├── feedback/        ← K. 위반 탐지 + 규칙 진화
├── lib/             ← L. 공유 셸 유틸리티
├── docs/            ← M. 가이드 문서
└── CLAUDE.md        ← N. 전역 Claude 행동 지침
```

---

## 의도별 파일 지도

### AI 행동 바꾸기 (질문·분석·판단 방식)

| 바꾸고 싶은 것 | 파일 | 영역 |
|-------------|------|------|
| 인터뷰어가 특정 질문을 하게 | `agents/interviewer.md` | A |
| Seed YAML 필드·구조 추가 | `agents/seed-architect.md` | A |
| 평가 체크리스트 항목 추가 | `agents/evaluator.md` | A |
| 평가 커맨드 흐름 변경 | `commands/evaluate.md` | B |
| 아키텍트 진단 기준 변경 | `agents/architect.md` | A |
| 코드 단순화 기준 변경 | `agents/simplifier.md` | A |
| 조사·참고 우선순위 변경 | `agents/researcher.md` | A |
| hacker 우회책 제약 추가 | `agents/hacker.md` | A |
| 반대 시나리오 기준 추가 | `agents/contrarian.md` | A |
| 도메인 용어 정의 방식 변경 | `agents/ontologist.md` | A |
| 테스트 설계 기준 변경 | `agents/test-designer.md` | A |
| 태스크 분해 방식 변경 | `commands/decompose.md` | B |
| 기술 설계서 섹션 추가 | `commands/trd.md` | B |
| 구현 실행 단계 변경 | `commands/run.md` | B |
| 구현 롤백 전략 변경 | `commands/rollback.md` | B |
| 막힌 상황 대응 방식 변경 | `commands/unstuck.md` | B |

### 자동 차단·검사 추가 (코드 패턴 탐지)

| 바꾸고 싶은 것 | 파일 | 영역 |
|-------------|------|------|
| 비밀번호·키 탐지 수정 | `gates/check-secrets.sh` | C |
| 보안 취약점 탐지 수정 | `gates/check-security.sh` | C |
| 레이어 경계 탐지 수정 | `gates/check-boundaries.sh` | C |
| 3-tier 위반 탐지 수정 | `gates/check-layers.sh` | C |
| AI 안티패턴 탐지 수정 | `gates/check-ai-antipatterns.sh` | C |
| 코드 복잡도 기준 변경 | `gates/check-complexity.sh` | C |
| 외과적 변경 탐지 수정 | `gates/check-surgical-changes.sh` | C |
| 신규 패턴 탐지 게이트 생성 | `gates/check-[name].sh` 신규 생성 | C |
| 게이트 전체 실행 순서 변경 | `feedback/detect-violations.sh` | K |
| 게이트 설치 스크립트 수정 | `gates/install-hooks.sh` | C |

### 허용·금지 규칙 (선언적)

| 바꾸고 싶은 것 | 파일 | 영역 |
|-------------|------|------|
| 레이어 간 import 금지 추가 | `gates/rules/boundaries.yaml` | D |
| 폴더 구조 규칙 추가 | `gates/rules/structure.yaml` | D |
| 권한 프리셋 수정 (strict/standard/permissive) | `boundaries/presets/[name].json` | E |

### 훅·자동화

| 바꾸고 싶은 것 | 파일 | 영역 |
|-------------|------|------|
| pre-commit 훅 변경 | `boundaries/hooks/pre-commit-gate.sh` | E |
| 편집 후 린트 훅 변경 | `boundaries/hooks/post-edit-lint.sh` | E |

### Claude 전역 행동 지침

| 바꾸고 싶은 것 | 파일 | 영역 |
|-------------|------|------|
| 절대 지켜야 할 규칙 추가 | `CLAUDE.md` → `## Absolute Rules` | N |
| AI 코딩 원칙 변경 | `CLAUDE.md` → `## AI Behavioral Baseline` | N |
| 레이어 정의 변경 | `CLAUDE.md` → `## Architecture Layers` | N |
| 워크플로우 순서 변경 | `CLAUDE.md` → `## Development Workflow` | N |

### 프로젝트 초기화 템플릿

| 바꾸고 싶은 것 | 파일 | 영역 |
|-------------|------|------|
| 새 프로젝트 CLAUDE.md 초기 내용 | `templates/CLAUDE.md.hbs` | I |
| 아키텍처 불변 규칙 초기 내용 | `templates/architecture-invariants.md.hbs` | I |
| 코드 컨벤션 초기 내용 | `templates/code-convention.yaml` | I |
| ADR 초기 내용 | `templates/adr.yaml` | I |

### 방법론별 변경

| 바꾸고 싶은 것 | 파일 | 영역 |
|-------------|------|------|
| BDD | `methodologies/bdd/` | F |
| TDD | `methodologies/tdd-strict/` | F |
| DDD Lite | `methodologies/ddd-lite/` | F |
| Shape Up | `methodologies/shape-up/` | F |
| Lean MVP | `methodologies/lean-mvp/` | F |
| Strangler Fig | `methodologies/strangler-fig/` | F |
| Parallel Change | `methodologies/parallel-change/` | F |
| RFC-driven | `methodologies/rfc-driven/` | F |
| Observability First | `methodologies/observability-first/` | F |
| Incident Review | `methodologies/incident-review/` | F |
| Exploration (Spike) | `methodologies/exploration/` | F |
| Living Spec | `methodologies/living-spec/` | F |
| Mikado Method | `methodologies/mikado-method/` | F |
| 방법론 레지스트리 수정 | `methodology/_registry.yaml` | H |

### Seed·Ouroboros

| 바꾸고 싶은 것 | 파일 | 영역 |
|-------------|------|------|
| Seed YAML 전체 구조 변경 | `ouroboros/templates/seed-spec.yaml` | G |
| Seed 최소 구조 변경 | `ouroboros/templates/seed-spec-minimal.yaml` | G |
| 모호성 체크 항목 추가 | `ouroboros/scoring/ambiguity-checklist.yaml` | G |

### Pro 기능 (Python)

| 바꾸고 싶은 것 | 파일 | 영역 |
|-------------|------|------|
| 드리프트 모니터링 로직 | `pro/src/harness_pro/drift/monitor.py` | J |
| 평가 파이프라인 로직 | `pro/src/harness_pro/evaluation/pipeline.py` | J |
| 인터뷰 엔진 로직 | `pro/src/harness_pro/interview/engine.py` | J |
| 모호성 스코어링 로직 | `pro/src/harness_pro/scoring/ambiguity.py` | J |
| Pro 훅 (session-start 등) | `pro/hooks/` | J |

### 규칙 진화·피드백

| 바꾸고 싶은 것 | 파일 | 영역 |
|-------------|------|------|
| 규칙 진화 가이드 수정 | `feedback/evolve-rules.md` | K |
| 위반 탐지 스크립트 | `feedback/detect-violations.sh` | K |

---

## 영역 코드 요약

| 코드 | 폴더 | 한 줄 설명 |
|------|------|----------|
| A | `agents/` | AI가 어떻게 생각하고 말하는가 |
| B | `commands/` | 워크플로우 단계와 순서 |
| C | `gates/` | 자동 탐지 및 차단 스크립트 |
| D | `gates/rules/` | 선언적 허용·금지 목록 |
| E | `boundaries/` | 훅·권한 프리셋 |
| F | `methodologies/` | 개발 방법론별 커맨드·게이트 |
| G | `ouroboros/` | Seed 템플릿·모호성 기준 |
| H | `methodology/` | 방법론 레지스트리·상태 |
| I | `templates/` | 프로젝트 초기화 템플릿 |
| J | `pro/` | Pro 기능 (Python) |
| K | `feedback/` | 위반 탐지·규칙 진화 |
| L | `lib/` | 공유 셸 유틸리티 |
| M | `docs/` | 가이드 문서 |
| N | `CLAUDE.md` | Claude 전역 행동 지침 |

---

## 에이전트 사용 지침

1. 요구사항이 들어오면 이 파일에서 해당 행을 찾는다
2. 해당 파일만 Read — 전체 스캔 금지
3. 찾지 못하면 영역 코드로 좁혀 해당 폴더를 Glob으로 탐색
