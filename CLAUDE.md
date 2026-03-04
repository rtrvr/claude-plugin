# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Claude Code 플러그인 모음 저장소. 각 플러그인은 Claude Code의 slash command로 동작하며, 한국어 중심으로 설계되어 있다.

## Architecture

```
.claude-plugin/marketplace.json   # 플러그인 레지스트리 (루트)
<plugin-name>/
├── .claude-plugin/plugin.json    # 플러그인 메타데이터
├── commands/                     # slash command 정의 (.md 파일)
└── skills/                       # 스킬 정의 (있는 경우)
    └── <skill-name>/
        ├── SKILL.md              # 스킬 워크플로우 정의
        └── *.md                  # 참조 문서 (knowledge base)
```

### 플러그인 목록

| 플러그인 | 설명 | 커맨드 |
|---------|------|--------|
| `commit` | tracked 파일만 안전하게 커밋 (한국어 메시지) | `commit-git-changes` |
| `pr` | PR 생성 및 리뷰 코멘트 해결 | `create-pr`, `resolve-pr` |
| `plan-to-docs` | 계획을 `.docs/` 구조 문서로 변환 | `make-plan-to-docs`, `implement-phase` |
| `marketing` | Meta Ads 계정 구조 컨설팅 | `meta-ads-consult` |
| `mistakes` | 실수 패턴 기록 및 접근 방식 재고 | `add-mistake`, `rethink` |
| `handoff` | 세션 핸드오프 프롬프트 생성 | `handoff` |
| `preflight` | 구현 전 의도 파악 및 확인 | `preflight` |
| `code-explorer` | pillo 코드 탐색 및 비개발자 질문 | `explore-code` |

## Conventions

- 모든 커맨드 파일(.md)은 frontmatter(`---`)로 시작하며 `description`, `allowed-tools`, `argument-hint`를 정의
- 플러그인 응답과 커밋 메시지는 한국어로 작성
- 커밋 시 `git add .` 사용 금지 — `git add -u` 또는 파일 명시
- `marketplace.json`에 새 플러그인 등록 필요
- 커밋 메시지 형식: `type(scope): 한국어 설명` (conventional commits)

## Adding a New Plugin

1. `<plugin-name>/.claude-plugin/plugin.json` 생성 (name, version, description, author)
2. `<plugin-name>/commands/<command>.md` 생성 (frontmatter + 프롬프트)
3. 루트 `.claude-plugin/marketplace.json`의 `plugins` 배열에 등록
4. 스킬이 필요하면 `<plugin-name>/skills/<skill-name>/SKILL.md` 추가
