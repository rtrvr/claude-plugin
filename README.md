# pitcrew

Claude Code 플러그인 모음. 각 플러그인은 slash command(`/`)로 호출하며, 한국어 중심으로 설계되어 있다.

## 설치

프로젝트의 `.claude/settings.json`에 플러그인 저장소를 등록한다:

```json
{
  "permissions": {
    "allow": [
      "Skill(simplify, *)"
    ]
  },
  "projects": {
    "/path/to/your/project": {
      "mcpServers": {},
      "pluginSources": [
        "/path/to/claude-plugin/.claude-plugin/marketplace.json"
      ]
    }
  }
}
```

`pluginSources`에 이 저장소의 `marketplace.json` 경로를 추가하면 모든 플러그인이 활성화된다.

## 플러그인 목록

| 플러그인 | 커맨드 | 설명 |
|---------|--------|------|
| commit | `/commit-git-changes` | tracked 파일만 안전하게 커밋 (한국어 메시지) |
| pr | `/create-pr`, `/resolve-pr` | PR 생성 및 리뷰 코멘트 해결 |
| plan-to-docs | `/make-plan-to-docs`, `/implement-phase` | 계획을 `.docs/` 구조 문서로 변환 및 Phase별 구현 |
| marketing | `/meta-ads-consult` | Meta Ads 계정 구조 컨설팅 |
| mistakes | `/add-mistake`, `/rethink` | 실수 패턴 기록 및 접근 방식 재고 |
| handoff | `/handoff` | 세션 핸드오프 프롬프트 생성 |
| preflight | `/preflight` | 구현 전 의도 파악 및 확인 |

## 언제 어떤 플러그인을 사용하는가

- **commit** — 코드 변경 후 커밋할 때. untracked 파일 실수 방지, 한국어 메시지 자동 생성
- **pr** — PR을 만들거나(`/create-pr`), 리뷰 코멘트를 처리할 때(`/resolve-pr`)
- **plan-to-docs** — 큰 작업을 시작하기 전 계획을 문서화(`/make-plan-to-docs`)하고, Phase별로 구현(`/implement-phase`)할 때
- **marketing** — Meta Ads 월 예산과 업종 기반 캠페인 구조 전략이 필요할 때
- **mistakes** — 반복되는 실수를 기록(`/add-mistake`)하거나, 현재 접근 방식이 맞는지 근거 기반으로 재검토(`/rethink`)할 때
- **handoff** — 세션을 마무리하면서 다음 세션에 맥락을 넘길 프롬프트를 생성할 때
- **preflight** — 요구사항이 모호한 작업을 시작하기 전, 소크라테스식 질문으로 의도를 명확히 할 때

## 플러그인 추가 방법

### 1. 플러그인 메타데이터 생성

`<plugin-name>/.claude-plugin/plugin.json`:

```json
{
  "name": "my-plugin",
  "version": "0.1.0",
  "description": "플러그인 설명",
  "author": "작성자"
}
```

### 2. 커맨드 파일 생성

`<plugin-name>/commands/<command-name>.md`:

```markdown
---
description: 커맨드가 하는 일을 한 줄로 설명
argument-hint: <필요한 인자 설명 (optional)>
allowed-tools: Read, Glob, Grep
---

# 커맨드 제목

프롬프트 내용을 여기에 작성합니다.

**입력**: $ARGUMENTS
```

### 3. marketplace.json에 등록

루트 `.claude-plugin/marketplace.json`의 `plugins` 배열에 추가:

```json
{
  "name": "my-plugin",
  "source": "./my-plugin",
  "description": "플러그인 설명"
}
```

### 4. (선택) 스킬 추가

복잡한 워크플로우가 필요하면 `<plugin-name>/skills/<skill-name>/SKILL.md`를 추가한다.

## 프로젝트 구조

```
.claude-plugin/marketplace.json     # 플러그인 레지스트리
commit/                             # 커밋 플러그인
├── .claude-plugin/plugin.json
└── commands/
pr/                                 # PR 플러그인
├── .claude-plugin/plugin.json
└── commands/
plan-to-docs/                       # 계획→문서 플러그인
├── .claude-plugin/plugin.json
├── commands/
└── skills/
marketing/                          # 마케팅 컨설팅 플러그인
├── .claude-plugin/plugin.json
├── commands/
└── skills/
mistakes/                           # 실수 기록 플러그인
├── .claude-plugin/plugin.json
└── commands/
handoff/                            # 세션 핸드오프 플러그인
├── .claude-plugin/plugin.json
└── commands/
preflight/                          # 사전 의도 파악 플러그인
├── .claude-plugin/plugin.json
└── commands/
```

## 컨벤션

- 모든 커맨드 파일(`.md`)은 frontmatter(`---`)로 시작하며 `description`, `allowed-tools`, `argument-hint`를 정의
- 플러그인 응답과 커밋 메시지는 **한국어**로 작성
- 커밋 시 `git add .` 사용 금지 — `git add -u` 또는 파일 명시
- 커밋 메시지 형식: `type(scope): 한국어 설명` (conventional commits)
