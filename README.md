# harness-customizer

> [ai-harness-template](https://github.com/studioKjm/ai-harness-template) 커스터마이징 보조 도구.
>
> 개발자가 자연어로 요구사항을 입력하면, 하네스 템플릿의 어느 파일을 어떻게 수정해야 하는지 찾고, Before/After 시뮬레이션으로 효과를 보여준 뒤 승인 시 실제 파일에 적용합니다.

---

## 이게 왜 필요한가

`ai-harness-template`은 팀·조직마다 커스터마이징이 필요합니다. 인터뷰어가 특정 질문을 하도록, 게이트가 특정 패턴을 차단하도록, 절대 규칙을 추가하도록. 그런데:

- 파일이 많아서 **어디를 고쳐야 하는지 모른다**
- 고쳤을 때 **실제로 효과가 있는지 모른다**
- **변경 이력**이 쌓이면 무엇이 base와 다른지 파악하기 어렵다

이 봇이 그 세 가지를 해결합니다.

---

## 구조

```
harness-customizer/
├── CLAUDE.md                        ← 설정 (BASE_HARNESS_PATH)
├── HARNESS-MAP.md                   ← 템플릿 파일 탐색 지도
├── customization-log.md             ← 변경 이력 (자동 누적)
├── KNOWLEDGE-GAPS.md                ← 도메인 지식 부족 추적
├── domain-knowledge/                ← 사내 도메인 지식 주입 폴더
│   └── README.md
└── .claude/
    ├── agents/
    │   ├── harness-navigator.md     ← 요구사항 → 대상 파일 탐색
    │   ├── harness-customizer.md    ← 수정 제안 + 승인 처리 + 로그
    │   ├── harness-simulator.md     ← Before/After 시뮬레이션
    │   └── harness-judge.md         ← 효과 판정 + 충분성 평가
    └── commands/
        └── customize.md             ← /customize 워크플로우 오케스트레이터
```

### 에이전트 역할 분담

| 에이전트 | 역할 |
|---------|------|
| `harness-navigator` | HARNESS-MAP.md 기반으로 수정 대상 파일 식별. 사내 도메인 키워드 감지 시 domain-knowledge/ 확인 |
| `harness-customizer` | 파일 Read 후 수정 내용 제안. 승인 처리 및 customization-log.md 기록 |
| `harness-simulator` | 실제 파일 수정 없이 Before/After 응답 차이를 시뮬레이션 |
| `harness-judge` | 요구사항 달성 Pass/Fail 판정. 도메인 지식 부족 시 KNOWLEDGE-GAPS.md 기록 |

---

## 워크플로우

```
/customize 입력
     │
     ▼
[Step 0~1] harness-navigator
  도메인 키워드 감지 → domain-knowledge/ 확인
  HARNESS-MAP.md → 대상 파일 식별
     │
     ▼
[Step 2] harness-customizer
  대상 파일 Read → 수정 내용 제안
  (삽입 위치 + 추가 내용 + 이유)
     │
     ▼
[Step 3] harness-simulator
  Before 시뮬레이션 → After 시뮬레이션
  (파일 미수정, LLM 예측)
     │
     ▼
[Step 4] harness-judge
  효과 판정: Pass → Step 5 / Fail → Step 2 재시도
     │
     ▼
[Step 5] harness-customizer
  적용하시겠습니까? (yes / 수정 요청 / 취소)
  yes → 실제 파일 수정 + customization-log.md 업데이트
     │
     ▼
[Step 6] harness-judge
  충분성 평가 → 도메인 부족 시 KNOWLEDGE-GAPS.md 기록
```

---

## 시나리오 예시

### 예시 1 — 에이전트 질문 추가

> "인터뷰어가 SSL/TLS 사설 CA 인증서 질문을 항상 하도록 하고 싶어"

- **탐색**: `agents/interviewer.md` (영역 A)
- **제안**: `## Mandatory Questions` 섹션 추가
- **Before**: SSL/TLS 질문 없음 → seed spec에 TLS 정책 누락 가능
- **After**: Score 무관하게 사설 CA 여부 항상 확인 → seed spec에 TLS 정책 포함

---

### 예시 2 — 외부 서비스 연동 정책 강제

> "메일 시스템은 반드시 행내 솔루션을 사용하도록, 외부 서비스는 인터페이스로 추상화 강제"

- **탐색**: `CLAUDE.md`, `agents/interviewer.md`, `agents/seed-architect.md`, `gates/check-security.sh`, `gates/rules/boundaries.yaml` (영역 A, C, D, N)
- **제안**:
  - CLAUDE.md Absolute Rules: "외부 서비스 연동은 반드시 인터페이스로 추상화"
  - interviewer.md: 외부 서비스 연동 감지 시 행내 솔루션 교체 여부 필수 질문
  - seed-architect.md: `provider_dev` / `provider_internal` 필드 강제
  - 신규 게이트: 외부 서비스 SDK의 도메인 레이어 직접 import 탐지 → 커밋 차단
- **핵심 원칙**: 외부망 개발(Mock) → 행내 배포(내부 솔루션) 구현체 교체 구조

---

### 예시 3 — 게이트 탐지 패턴 추가

> "API Key가 코드에 하드코딩되면 커밋 차단하고 싶어"

- **탐색**: `gates/check-secrets.sh` (영역 C)
- **Before**: 특정 패턴 탐지 못함 → 커밋 통과
- **After**: 패턴 추가 → ❌ GATE FAIL로 커밋 차단

---

### 예시 4 — 도메인 지식 주입 경로 질문 (라이브 테스트 통과)

> "취약성 검사가 필요하여 관련 정보를 주입하고 싶은데, 어느 경로에 어떤 내용을 적어야 할까? 솔루션 명까지 적어야 하나?"

봇의 답변:

- **경로**: `domain-knowledge/vulnerability-scanner.md` 신규 생성
- **포함할 내용**:
  - 솔루션명 (Checkmarx / Fortify / SonarQube 등) — **반드시 필요**. 솔루션마다 리포트 파싱 방식이 다름
  - 검사 유형 (SAST / SCA / DAST)
  - 반입 차단 기준 (Critical 1개 이상? High 3개 이상?)
  - 리포트 형식 (XML / JSON) 및 경로
- **연동 게이트**: `gates/check-security.sh` 수정 또는 `gates/check-vuln-policy.sh` 신규 생성 (영역 C)
- **솔루션명 없을 때**: 원칙 수준 답변만 가능 → KNOWLEDGE-GAPS.md에 자동 기록

---

## 설치 및 설정

### 1. ai-harness-template 클론

```bash
git clone https://github.com/studioKjm/ai-harness-template /path/to/your/ai-harness-template
```

### 2. BASE_HARNESS_PATH 설정

`CLAUDE.md`를 열어 `BASE_HARNESS_PATH`를 클론한 경로로 변경합니다:

```markdown
| **BASE_HARNESS_PATH** | `/path/to/your/ai-harness-template` |
```

이 값이 모든 에이전트의 파일 탐색 기준이 됩니다. **여기만 바꾸면 됩니다.**

### 3. Claude Code에서 사용

이 프로젝트 폴더를 Claude Code로 열고:

```
/customize
```

요구사항을 입력하면 워크플로우가 시작됩니다.

---

## 사내 도메인 지식 주입

사내 시스템(ESB, MFT, SSO, 내부 메일 솔루션 등)에 대한 정보가 있으면 시뮬레이션 정밀도가 올라갑니다.

```
domain-knowledge/
└── mail-solution.md     ← 예: 행내 메일 솔루션 이름, API 방식, 인증 방법
└── sso.md               ← 예: 행내 SSO 연동 방식
└── internal-ca.md       ← 예: 사설 CA 인증서 경로, 신뢰 방법
```

`domain-knowledge/README.md`에 파일 작성 가이드가 있습니다.

도메인 지식 없이도 동작하지만, 부족한 정보는 `KNOWLEDGE-GAPS.md`에 자동 기록됩니다.

---

## 변경 이력 관리

수정이 적용될 때마다 `customization-log.md`에 자동 누적됩니다:

- 변경 파일, 요구사항 요약, 영역 코드(A~N)
- 구조 변경(새 폴더/프로세스 추가) 시 AS-IS → TO-BE 기록
- 3개 이상 영역 변경 시 Diff 요약 자동 생성

---

## 현재 수준과 한계

### 할 수 있는 것
- 자연어 요구사항 → 14개 영역에서 대상 파일 자동 탐색
- 파일 타입별 Before/After 시뮬레이션 (agent md / command md / gate sh / yaml)
- 승인 시 실제 파일 수정 + 변경 이력 자동 누적
- 도메인 지식 주입 경로 및 필요 내용 안내
- 구조 변경 AS-IS → TO-BE 이력 관리

### 현재 한계 (아직 못하는 것)
| 항목 | 이유 |
|------|------|
| 시뮬레이션 100% 정확도 보장 | LLM 예측 기반. 게이트 스크립트 실제 실행 불가 |
| 행내 솔루션 정보 자동 수집 | 사내 시스템 접근 불가. domain-knowledge/ 수동 작성 필요 |
| 솔루션명 없는 게이트 코드 생성 | 솔루션별 파싱 방식이 달라 특정 없이는 원칙 수준만 가능 |
| 대규모 구조 리팩토링 일괄 처리 | 요구사항 1건 = 파일 1~2개 단위. 대규모는 단계별 진행 필요 |

---

## 소개 자료

- `harness-customizer-intro.pptx` — 개념·구조·시나리오·한계를 담은 14슬라이드 발표 자료

---

## Base 템플릿

- GitHub: [studioKjm/ai-harness-template](https://github.com/studioKjm/ai-harness-template)
