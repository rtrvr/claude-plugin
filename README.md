# pitcrew

Claude Code 플러그인 모음. 각 플러그인은 slash command(`/`)로 호출하며, 한국어 중심으로 설계되어 있다.

## 설치

### 빠른 설치 (권장)

Claude Code에서 아래 명령어를 순서대로 입력한다.

**1단계: 마켓플레이스 등록**

```
/plugin marketplace add <저장소 경로>
```

- 로컬 경로: `/Users/you/claude-plugin/.claude-plugin/marketplace.json`
- Git URL: `https://github.com/owner/claude-plugin.git`

**2단계: 원하는 플러그인 설치**

```
/plugin install commit@pitcrew
```

전체 플러그인을 한번에 설치하려면:

```
/plugin install commit@pitcrew
/plugin install pr@pitcrew
/plugin install plan-to-docs@pitcrew
/plugin install marketing@pitcrew
/plugin install mistakes@pitcrew
/plugin install handoff@pitcrew
/plugin install preflight@pitcrew
```

**3단계: 사용**

```
/commit:commit-git-changes
/pr:create-pr
```

### 플러그인 관리

| 작업 | 명령어 |
|------|--------|
| 설치된 플러그인 확인 | `/plugin` → Installed 탭 |
| 비활성화 | `/plugin disable <plugin-name>` |
| 삭제 | `/plugin uninstall <plugin-name>` |
| 업데이트 | `/plugin marketplace update` |

### 수동 설치

팀 전체에 동일 플러그인을 적용하려면 프로젝트의 `.claude/settings.json`에 직접 등록한다:

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

### commit — 안전한 커밋

이럴 때 사용:
- 코드 수정 후 커밋하고 싶을 때
- `git add .` 실수로 불필요한 파일이 커밋되는 걸 방지하고 싶을 때
- 한국어 conventional commit 메시지를 자동으로 만들고 싶을 때

```
/commit:commit-git-changes
```

이 플러그인이 하는 일:
- `git add -u`로 tracked 파일만 스테이징 (untracked 파일이 있으면 경고)
- diff 내용을 검사하여 민감 정보(API 키, 비밀번호 등) 포함 여부 체크
- `type(scope): 한국어 설명` 형식의 커밋 메시지 자동 생성

---

### pr — PR 생성 및 리뷰 처리

이럴 때 사용:
- 작업 브랜치에서 PR을 만들고 싶을 때
- PR에 달린 리뷰 코멘트를 하나씩 처리하고 싶을 때

```
/pr:create-pr
/pr:resolve-pr
```

이 플러그인이 하는 일:
- **create-pr**: 베이스 브랜치를 자동 추론하고, 커밋을 타입별로 그룹핑하여 PR 본문 생성. 자동 push 후 브라우저에서 PR 페이지 오픈
- **resolve-pr**: 리뷰 코멘트를 우선순위(P1~P4)로 분류하고 하나씩 수정

---

### plan-to-docs — 계획을 문서로, 문서를 코드로

이럴 때 사용:
- "로그인 기능 추가해야 해"처럼 막연한 계획을 체계적으로 정리하고 싶을 때
- 큰 작업을 Phase별로 나눠서 구현하고 싶을 때
- 팀원에게 작업 계획을 공유할 문서가 필요할 때

```
/plan-to-docs:make-plan-to-docs
/plan-to-docs:implement-phase
```

이 플러그인이 하는 일:
- **make-plan-to-docs**: 막연한 계획을 `.docs/` 폴더에 구조화된 문서(README, Phase별 파일, 기술 스펙)로 변환
- **implement-phase**: 문서의 Phase를 하나씩 구현하면서 진행률 자동 업데이트

---

### marketing — Meta Ads 캠페인 전략

이럴 때 사용:
- Meta(Facebook/Instagram) 광고를 시작하려는데 캠페인 구조를 어떻게 짜야 할지 모를 때
- 월 광고 예산에 맞는 단계별 전략이 필요할 때

```
/marketing:meta-ads-consult
```

이 플러그인이 하는 일:
- 월 광고 예산과 업종만 입력하면 2026 Meta Ads 3단계 전략(테스트 → 스케일 → Advantage+) 제공
- 예산 구간별 캠페인 구조, 타겟팅, 크리에이티브 전략 추천

---

### mistakes — 실수 기록 및 접근 방식 재검토

이럴 때 사용:
- 같은 실수를 반복하고 있다고 느낄 때
- "API 응답 구조를 확인 안 하고 코드 짰다"처럼 실수를 기록해두고 싶을 때
- 현재 접근 방식이 맞는지 의심될 때

```
/mistakes:add-mistake
/mistakes:rethink
```

이 플러그인이 하는 일:
- **add-mistake**: 실수를 `.claude/MISTAKES.md`에 카테고리별로 기록
- **rethink**: 현재 접근 방식을 5 Whys와 다층 분석으로 재검토하고 대안 제시

---

### handoff — 세션 핸드오프

이럴 때 사용:
- 세션을 마무리하면서 다음 세션에 맥락을 넘기고 싶을 때
- 작업 도중 세션이 끊겼을 때 이어서 작업하고 싶을 때
- 다른 사람에게 작업을 인수인계할 때

```
/handoff:handoff
```

이 플러그인이 하는 일:
- 파일 경로, 현재 상태, 남은 작업, 기대 결과를 포함한 핸드오프 프롬프트 자동 생성
- 새 세션에 복사-붙여넣기하면 맥락 100% 전달

---

### preflight — 구현 전 의도 파악

이럴 때 사용:
- 요구사항이 모호해서 바로 구현하면 방향이 틀어질 것 같을 때
- 구현 전에 "정확히 뭘 만들어야 하는지" 확인하고 싶을 때

```
/preflight:preflight
```

이 플러그인이 하는 일:
- 소크라테스식 질문으로 의도를 파악
- "저장 버튼 누르면 2초간 체크마크 표시"처럼 구체적 예시로 확인 후 구현 시작

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
