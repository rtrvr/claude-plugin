---
description: pillo 프로젝트(Android/iOS) 코드를 가져오고, 비개발자가 코드에 대해 자유롭게 질문할 수 있습니다.
allowed-tools: Bash, Read, Glob, Grep, AskUserQuestion
argument-hint: <질문 또는 android/ios 키워드>
---

pillo 프로젝트 코드 탐색 요청을 처리한다. `$ARGUMENTS`를 기반으로 진행한다.

- `skills/code-explorer/SKILL.md`를 읽고 워크플로우를 따른다
- `skills/code-explorer/repo-config.md`를 읽어 저장소 정보를 확인한다
- `$ARGUMENTS`가 있으면 저장소 힌트 탐지 및 질문 처리를 함께 수행한다
- `$ARGUMENTS`가 비어 있으면 저장소 선택부터 시작한다
- 비개발자가 이해할 수 있는 쉬운 표현으로 작성한다
- 이 플러그인은 **읽기 전용**이다 — 코드를 수정하지 않는다
