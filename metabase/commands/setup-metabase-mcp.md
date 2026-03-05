---
description: Metabase MCP 서버를 Claude Code에 연결하는 설정 가이드. Claude가 Metabase에 직접 접근해 쿼리 실행, 대시보드 조회/생성 등을 할 수 있게 됩니다.
allowed-tools: Bash, AskUserQuestion
argument-hint: (없어도 됨 — 대화형으로 진행)
---

Metabase MCP 서버 설정을 도와준다. 아래 순서대로 진행한다.

## Step 1: 필수 정보 수집

`$ARGUMENTS`에서 Metabase URL과 API 키를 파악한다. 없으면 `AskUserQuestion`으로 확인:

1. **Metabase URL** — 예: `http://metabase.example.com` 또는 WireGuard 경유 내부 IP
2. **Metabase API 키** — Metabase 관리자 설정 → API Keys에서 발급

API 키 발급 방법을 모르면 안내한다:
```
Metabase 접속 → 우측 상단 프로필 아이콘 → Admin settings → API Keys → Create API Key
```

## Step 2: MCP 서버 등록

아래 명령어로 Claude Code에 MCP 서버를 등록한다. URL과 API 키를 실제 값으로 대체한다.

```bash
claude mcp add metabase \
  -e METABASE_URL=<METABASE_URL> \
  -e METABASE_API_KEY=<METABASE_API_KEY> \
  -- npx -y metabase-mcp
```

**범위 선택 안내:**
- `--scope local` (기본): 현재 프로젝트에서만 사용
- `--scope user`: 모든 프로젝트에서 사용 (권장)

전체 명령어 예시 (user 범위):
```bash
claude mcp add metabase \
  --scope user \
  -e METABASE_URL=http://10.0.0.1:3000 \
  -e METABASE_API_KEY=mb_xxxxxxxxxxxx \
  -- npx -y metabase-mcp
```

## Step 3: 설정 확인

```bash
claude mcp list
```

`metabase`가 목록에 표시되면 성공.

## Step 4: 연결 테스트

Claude Code를 **재시작**한 뒤 새 세션에서 아래를 테스트한다:

```
/metabase-query 연결된 데이터베이스 목록 보여줘
```

정상이면 Metabase의 데이터베이스 목록이 출력된다.

## 문제 해결

| 증상 | 해결 |
|------|------|
| `npx: command not found` | Node.js 설치 필요: `brew install node` |
| 인증 오류 | API 키 재확인. Metabase URL 끝에 `/` 없는지 확인 |
| 연결 거부 | Metabase가 실행 중인지, WireGuard 연결 상태 확인 |
| MCP 미표시 | Claude Code 재시작 후 재시도 |
