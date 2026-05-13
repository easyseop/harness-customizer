# Customization Log

> Base 템플릿 대비 변경 이력.
> `/customize` 로 변경이 승인·적용될 때마다 에이전트가 이 파일에 기록한다.

---

## 변경 이력

| # | 날짜 | 요구사항 요약 | 변경 파일 | 영역 | 유형 |
|---|------|------------|---------|------|------|
| — | — | 아직 변경 없음 | — | — | — |

<!-- 기록 형식:
| 1 | 2026-05-14 | 인터뷰어가 MFT 질문하게 | .claude/agents/interviewer.md | A | 수정 |
|   | | └ 이유: 파일 전송 기능이 있는 경우 행내 MFT 솔루션 여부를 인터뷰 단계에서 파악해야 seed에 반입 제약이 담기기 때문 | | | |
-->

---

## 영역별 누적 변경 수

| 코드 | 폴더 | 변경 수 |
|------|------|--------|
| A | `agents/` | 0 |
| B | `commands/` | 0 |
| C | `gates/` | 0 |
| D | `gates/rules/` | 0 |
| E | `boundaries/` | 0 |
| F | `methodologies/` | 0 |
| G | `ouroboros/` | 0 |
| I | `templates/` | 0 |
| J | `pro/` | 0 |
| K | `feedback/` | 0 |
| N | `CLAUDE.md` | 0 |
| **합계** | — | **0** |

---

## Base 대비 변경 파일 목록

> 변경이 적용된 파일이 여기 누적된다.
> 3개 영역 이상에 변경이 쌓이면 에이전트가 자동으로 Diff 요약을 출력한다.

*(없음)*

---

## 구조 변경 이력

> 새 폴더, 에이전트, 프로세스 단계가 추가될 때마다 AS-IS → TO-BE로 기록한다.

| # | 날짜 | 변경 내용 요약 | 추가 이유 |
|---|------|-------------|---------|
| 1 | 2026-05-14 | 에이전트 단일 파일 → 4개 분리 | harness-customizer.md 과부하로 성능 저하 우려 |
|   | | └ AS-IS: harness-customizer (단일) | |
|   | | └ TO-BE: navigator → customizer → simulator → judge | |
| 2 | 2026-05-14 | domain-knowledge/ 폴더 + Phase 0 추가 | 사내 도메인 지식 주입 경로 필요 |
|   | | └ AS-IS: Step1(탐색) → Step2(제안) → Step3(시뮬) → Step4(판정) | |
|   | | └ TO-BE: Step0(도메인확인) → Step1 → Step2 → Step3 → Step4 → Step5(승인) → Step6(충분성) | |
| 3 | 2026-05-14 | KNOWLEDGE-GAPS.md + Phase 8(충분성평가) 추가 | 도메인 부족 시 누락 추적 필요 |
|   | | └ AS-IS: 판정 후 종료 | |
|   | | └ TO-BE: 판정 → 충분성 평가 → KNOWLEDGE-GAPS.md 기록 | |

---

## Diff 요약 기록

> 누적 변경이 3개 영역을 넘을 때마다 스냅샷이 여기 추가된다.

*(없음)*
