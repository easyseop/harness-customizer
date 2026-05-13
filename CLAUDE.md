# harness-customizer

하네스 템플릿 커스터마이징 보조 도구.

개발자가 자연어로 요구사항을 말하면:
1. 어느 agent/command md를 수정해야 하는지 찾아주고
2. 무엇을 추가해야 하는지 제안하고
3. 수정 전/후 시뮬레이션으로 효과를 보여준다

## 설정

> **처음 사용 시**: `BASE_HARNESS_PATH`를 GitHub 레포를 클론한 로컬 경로로 변경하세요.
> GitHub: https://github.com/studioKjm/ai-harness-template

| 항목 | 값 |
|------|-----|
| **BASE_HARNESS_PATH** | `/path/to/your/ai-harness-template` |
| agents 폴더 | `{BASE_HARNESS_PATH}/.claude/agents/` |
| commands 폴더 | `{BASE_HARNESS_PATH}/.claude/commands/` |

모든 에이전트는 경로가 필요할 때 이 파일의 `BASE_HARNESS_PATH`를 읽어서 사용한다.
에이전트 파일 내부의 경로는 참고용이며, CLAUDE.md가 우선한다.

## 사용법

```
/customize
```

요구사항을 입력하면 워크플로우가 시작된다.

## 절대 규칙

- 실제 파일 수정은 개발자가 명시적으로 승인한 뒤에만 한다
- 시뮬레이션 결과는 LLM 예측이므로 100% 보장이 아님을 명시한다
- 하나의 요구사항당 하나의 수정 단위로 처리한다
