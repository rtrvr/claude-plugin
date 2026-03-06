# Metabase Skill Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** 비개발자가 자연어로 질문하면 BigQuery SQL 쿼리를 생성하거나 Metabase 시각화 전략을 안내하는 Claude Code 플러그인 스킬을 만든다.

**Architecture:** 기존 `marketing` 플러그인 패턴을 따라 `SKILL.md`(워크플로우) + `schema.md`(BigQuery 테이블 스키마)로 구성한다. 커맨드(`metabase-query.md`)가 스킬을 트리거하고, 스킬이 질문 의도를 SQL/시각화로 자동 분류해 응답한다.

**Tech Stack:** Claude Code plugin system (Markdown-based), BigQuery SQL, Metabase

---

### Task 1: 플러그인 메타데이터 생성

**Files:**
- Create: `metabase/.claude-plugin/plugin.json`

**Step 1: 디렉토리 생성 및 plugin.json 작성**

```json
{
  "name": "metabase",
  "version": "1.0.0",
  "description": "비개발자를 위한 Metabase SQL 쿼리 생성 및 시각화 가이드. 자연어로 질문하면 BigQuery SQL 또는 차트 설정 방법을 안내합니다.",
  "author": "pitcrew"
}
```

파일 경로: `metabase/.claude-plugin/plugin.json`

**Step 2: 파일 생성 확인**

```bash
cat metabase/.claude-plugin/plugin.json
```

Expected: JSON 내용 출력

**Step 3: Commit**

```bash
git add metabase/.claude-plugin/plugin.json
git commit -m "feat(metabase): 플러그인 메타데이터 추가"
```

---

### Task 2: BigQuery 스키마 참조 문서 작성

**Files:**
- Create: `metabase/skills/metabase/schema.md`

**Step 1: 디렉토리 생성**

```bash
mkdir -p metabase/skills/metabase
```

**Step 2: schema.md 작성**

아래 내용으로 `metabase/skills/metabase/schema.md` 파일을 생성한다.

```markdown
# Pillo BigQuery 스키마

프로젝트: `pillo-10a7d`
데이터셋: `analytics`
전체 경로 형식: `` `pillo-10a7d.analytics.<테이블명>` ``

---

## 테이블: events

앱에서 발생한 모든 이벤트 로그. **파티션 필터 필수** (`event_timestamp` 기준 DAY 파티션).

| 컬럼 | 타입 | 설명 |
|------|------|------|
| event_id | STRING | 이벤트 고유 ID (REQUIRED) |
| event_name | STRING | 이벤트 이름 (REQUIRED) |
| event_timestamp | TIMESTAMP | 이벤트 발생 시각 (REQUIRED, 파티션 키) |
| device_id | STRING | 디바이스 고유 ID (REQUIRED) |
| user_id | STRING | 로그인한 유저 ID (로그인 전 NULL) |
| session_id | STRING | 세션 ID |
| platform | STRING | 'ios' 또는 'android' |
| properties | JSON | 이벤트별 추가 속성 (JSON_VALUE로 접근) |
| app_version | STRING | 앱 버전 |
| os_version | STRING | OS 버전 |
| device_model | STRING | 기기 모델명 |
| country | STRING | 국가 코드 |
| city | STRING | 도시 |
| received_at | TIMESTAMP | 서버 수신 시각 |
| client_timestamp | TIMESTAMP | 클라이언트 발생 시각 |

**클러스터링:** event_id, event_name, device_id

**특수 이벤트:**
- `$identify` — 로그인/회원가입 시 발생. `properties['$set']`에 유저 속성 포함.

**properties 접근 패턴:**
```sql
JSON_VALUE(properties, '$.key_name')
```

---

## 테이블: identity_map

device_id ↔ user_id 매핑 테이블. 로그인 전(익명) → 로그인 후 동일 유저 추적에 사용.

| 컬럼 | 타입 | 설명 |
|------|------|------|
| device_id | STRING | 디바이스 ID (REQUIRED) |
| canonical_user_key | STRING | 통합 유저 키 (로그인 전: device_id, 후: user_id) |
| user_id | STRING | Firebase user ID (로그인 전 NULL) |
| first_seen_at | TIMESTAMP | 최초 발견 시각 |
| last_seen_at | TIMESTAMP | 최근 발견 시각 |
| user_id_set_at | TIMESTAMP | user_id가 처음 설정된 시각 (회원가입/로그인 시각으로 활용) |

**클러스터링:** canonical_user_key

---

## 테이블: user_profiles

유저별 집계 프로파일. identity_map 기반으로 1시간마다 갱신.

| 컬럼 | 타입 | 설명 |
|------|------|------|
| user_key | STRING | 통합 유저 키 (canonical_user_key와 동일) |
| user_id | STRING | Firebase user ID |
| device_count | INT64 | 연결된 디바이스 수 |
| properties | JSON | $identify 이벤트에서 수집된 유저 속성 누적 |
| first_seen_at | TIMESTAMP | 최초 앱 사용 시각 |
| last_seen_at | TIMESTAMP | 최근 앱 사용 시각 |
| created_at | TIMESTAMP | 프로파일 생성 시각 |
| updated_at | TIMESTAMP | 프로파일 마지막 갱신 시각 |

**클러스터링:** user_key

---

## 자주 쓰는 쿼리 패턴

### DAU (일별 활성 사용자)
```sql
SELECT
  DATE(event_timestamp) AS dt,
  COUNT(DISTINCT device_id) AS dau
FROM `pillo-10a7d.analytics.events`
WHERE event_timestamp >= TIMESTAMP('2026-01-01')  -- 파티션 필터 필수
GROUP BY dt
ORDER BY dt
```

### 신규 가입자 수
```sql
SELECT
  DATE(user_id_set_at) AS dt,
  COUNT(DISTINCT user_id) AS new_users
FROM `pillo-10a7d.analytics.identity_map`
WHERE user_id IS NOT NULL
  AND user_id_set_at >= TIMESTAMP('2026-01-01')
GROUP BY dt
ORDER BY dt
```

### 이벤트별 발생 횟수
```sql
SELECT
  event_name,
  COUNT(*) AS cnt
FROM `pillo-10a7d.analytics.events`
WHERE event_timestamp >= TIMESTAMP_SUB(CURRENT_TIMESTAMP(), INTERVAL 7 DAY)
GROUP BY event_name
ORDER BY cnt DESC
```

### user_id 기준 이벤트 조회 (identity_map JOIN)
```sql
SELECT
  e.event_name,
  e.event_timestamp,
  im.user_id
FROM `pillo-10a7d.analytics.events` AS e
JOIN `pillo-10a7d.analytics.identity_map` AS im
  ON e.device_id = im.device_id
WHERE e.event_timestamp >= TIMESTAMP_SUB(CURRENT_TIMESTAMP(), INTERVAL 7 DAY)
  AND im.user_id = 'TARGET_USER_ID'
```

---

## 주의사항

1. **파티션 필터 필수**: `events` 테이블 쿼리 시 반드시 `event_timestamp >= ...` 조건 포함. 없으면 전체 테이블 스캔으로 비용 폭증.
2. **properties는 JSON 타입**: `JSON_VALUE(properties, '$.key')` 또는 `properties.key` 형식으로 접근.
3. **익명/로그인 유저 구분**: `user_id IS NULL`이면 익명 유저. 로그인 유저만 보려면 `user_id IS NOT NULL`.
4. **갱신 주기**: `identity_map`과 `user_profiles`는 1시간마다 갱신. 실시간 데이터는 아님.
```

**Step 3: Commit**

```bash
git add metabase/skills/metabase/schema.md
git commit -m "feat(metabase): BigQuery 스키마 참조 문서 추가"
```

---

### Task 3: 스킬 워크플로우(SKILL.md) 작성

**Files:**
- Create: `metabase/skills/metabase/SKILL.md`

**Step 1: SKILL.md 작성**

아래 내용으로 `metabase/skills/metabase/SKILL.md`를 생성한다.

```markdown
---
name: metabase
description: Pillo BigQuery 데이터를 Metabase에서 쿼리하거나 시각화하는 방법을 안내하는 스킬. "쿼리 짜줘", "DAU 뽑아줘", "차트 어떻게 만들어", "대시보드" 등의 요청 시 사용한다.
---

# Metabase 쿼리 & 시각화 가이드

## Overview

비개발자가 Metabase에서 데이터를 뽑거나 차트를 만들 수 있도록 돕는 스킬.
- 자연어 질문 → BigQuery SQL 쿼리 생성 (Metabase Native Query에 붙여넣기용)
- 시각화 요청 → Metabase 차트 설정 단계별 안내

## Workflow

### Step 1: 의도 분류

`$ARGUMENTS`를 읽어 아래 기준으로 분류한다.

**SQL 생성 트리거** (다음 키워드가 포함된 경우):
- "몇 명", "얼마나", "몇 건", "몇 번", "집계", "쿼리", "뽑아", "조회", "DAU", "MAU", "리텐션", "가입자", "이벤트 수"

**시각화 가이드 트리거** (다음 키워드가 포함된 경우):
- "차트", "그래프", "대시보드", "시각화", "어떻게 보여", "어떻게 만들어"

**둘 다 포함되거나 모호한 경우**: SQL 먼저 생성 후 "이 쿼리로 차트도 만들 수 있습니다. 어떤 차트로 보고 싶으신가요?"로 연결.

### Step 2: 스키마 참조

`schema.md`를 읽어 질문에 관련된 테이블과 컬럼을 파악한다.

- 이벤트/행동 분석 → `events` 테이블
- 유저 수/가입자 → `identity_map` 또는 `user_profiles`
- 유저별 속성 → `user_profiles.properties`

### Step 3-A: SQL 생성 응답

아래 형식으로 응답한다.

```
## [질문 요약] 쿼리

**사용 테이블:** [테이블명]
**기간:** [쿼리에 적용된 기간]

```sql
[완성된 BigQuery SQL]
```

**Metabase에서 실행하는 방법:**
1. Metabase 접속 → 상단 "+ New" 클릭
2. "SQL query" 선택
3. 데이터베이스로 "BigQuery - pillo-10a7d" 선택
4. 위 쿼리를 붙여넣고 "Run" (▶) 클릭

**결과 설명:** [쿼리가 뭘 보여주는지 1-2줄 설명]
```

**SQL 작성 규칙:**
- `events` 테이블은 항상 `WHERE event_timestamp >= TIMESTAMP('...')` 파티션 필터 포함
- 기간이 명시 안 되면 최근 30일로 기본 설정
- user_id 기준 조회 시 `identity_map` JOIN 사용
- `properties` 접근은 `JSON_VALUE(properties, '$.key')` 패턴

### Step 3-B: 시각화 가이드 응답

아래 형식으로 응답한다.

```
## [질문 요약] 시각화 방법

**추천 차트 타입:** [Line / Bar / Number / Table / Pie 중 선택]
**추천 이유:** [왜 이 차트가 적합한지 1줄]

**Metabase 설정 단계:**
1. [단계별 Metabase UI 안내]
2. ...

**기반 쿼리 (필요시):**
[차트에 쓸 SQL이 필요한 경우 함께 제공]
```

**차트 타입 선택 가이드:**
- 시간별 추이 → Line Chart (x축: 날짜, y축: 수치)
- 카테고리 비교 → Bar Chart
- 단일 핵심 수치 → Number (KPI 카드)
- 상세 데이터 테이블 → Table
- 비율/구성 → Pie Chart (5개 이하 항목일 때만)

## 오류 처리

| 상황 | 응답 |
|------|------|
| 테이블/컬럼 불확실 | "어떤 데이터를 보고 싶으신지 조금 더 구체적으로 알려주시면 정확한 쿼리를 드릴 수 있어요. 예: '지난 주 DAU'" |
| 이벤트명 불확실 | "어떤 이벤트명인지 확인이 필요합니다. Metabase에서 SELECT DISTINCT event_name FROM ... 으로 목록을 확인해보세요." |
| 실시간 데이터 요청 | "identity_map과 user_profiles는 1시간마다 갱신됩니다. 실시간 데이터가 필요하면 events 테이블을 직접 조회하세요." |

## References

테이블 스키마 상세는 `schema.md`를 참조한다.
이벤트 택소노미(이벤트명 목록)는 추후 `taxonomy.md`에 추가 예정.
```

**Step 2: Commit**

```bash
git add metabase/skills/metabase/SKILL.md
git commit -m "feat(metabase): 스킬 워크플로우 정의"
```

---

### Task 4: 커맨드 파일 작성

**Files:**
- Create: `metabase/commands/metabase-query.md`

**Step 1: commands 디렉토리 생성 및 파일 작성**

```bash
mkdir -p metabase/commands
```

아래 내용으로 `metabase/commands/metabase-query.md`를 생성한다.

```markdown
---
description: Metabase에서 BigQuery 데이터를 조회하는 SQL 쿼리를 생성하거나, 차트/대시보드 만드는 방법을 안내합니다.
allowed-tools: Read
argument-hint: "[질문] (예: 어제 DAU 뽑아줘, 신규 가입자 추이 차트 만들고 싶어)"
---

Metabase 스킬을 사용해 아래 요청을 처리합니다: $ARGUMENTS

스킬 파일을 읽어 워크플로우를 따릅니다:
- SKILL.md: `~/.claude/plugins/<plugin-path>/skills/metabase/SKILL.md`
- schema.md: `~/.claude/plugins/<plugin-path>/skills/metabase/schema.md`
```

> **참고:** 커맨드에서 스킬 파일 경로는 실제 설치 경로에 따라 달라진다. 설치 후 경로를 확인해 수정이 필요할 수 있다. 다른 플러그인의 커맨드 파일 패턴을 참고한다.

**Step 2: 기존 커맨드 패턴 확인**

```bash
cat code-explorer/commands/explore-code.md
```

Expected: frontmatter + 스킬 참조 패턴 확인. 동일한 패턴으로 작성되었는지 대조.

**Step 3: Commit**

```bash
git add metabase/commands/metabase-query.md
git commit -m "feat(metabase): metabase-query 커맨드 추가"
```

---

### Task 5: marketplace.json 등록

**Files:**
- Modify: `.claude-plugin/marketplace.json`

**Step 1: marketplace.json에 metabase 플러그인 추가**

`plugins` 배열 마지막에 아래 항목을 추가한다.

```json
{
  "name": "metabase",
  "source": "./metabase",
  "description": "비개발자를 위한 Metabase 쿼리 & 시각화 가이드. 자연어 질문으로 BigQuery SQL 생성 또는 차트 설정 방법을 안내합니다."
}
```

**Step 2: JSON 유효성 확인**

```bash
cat .claude-plugin/marketplace.json | python3 -m json.tool
```

Expected: JSON 파싱 성공 (에러 없음)

**Step 3: Commit**

```bash
git add .claude-plugin/marketplace.json
git commit -m "feat(metabase): marketplace에 플러그인 등록"
```

---

### Task 6: 커맨드 파일 경로 패턴 수정

**Files:**
- Modify: `metabase/commands/metabase-query.md`

**Step 1: 기존 커맨드 패턴 확인**

```bash
cat code-explorer/commands/explore-code.md
cat marketing/commands/meta-ads-consult.md
```

Expected: 스킬을 어떻게 참조하는지 패턴 확인

**Step 2: 패턴에 맞게 metabase-query.md 수정**

기존 플러그인들이 스킬을 참조하는 방식과 동일하게 커맨드 파일을 수정한다.

**Step 3: Commit**

```bash
git add metabase/commands/metabase-query.md
git commit -m "fix(metabase): 커맨드 스킬 참조 경로 수정"
```

---

### Task 7: 수동 검증

**Step 1: 파일 구조 확인**

```bash
find metabase -type f | sort
```

Expected:
```
metabase/.claude-plugin/plugin.json
metabase/commands/metabase-query.md
metabase/skills/metabase/SKILL.md
metabase/skills/metabase/schema.md
```

**Step 2: marketplace.json 확인**

```bash
grep -A 4 '"metabase"' .claude-plugin/marketplace.json
```

Expected: metabase 항목이 포함된 JSON 출력

**Step 3: 전체 커밋 이력 확인**

```bash
git log --oneline -6
```

Expected: Task 1~6 커밋 6개 확인

---

## 향후 작업 (이번 플랜 범위 외)

- `metabase/skills/metabase/taxonomy.md` 추가 — 이벤트명 목록과 properties 키 정의
- 쿼리 패턴을 실사용 피드백 기반으로 보강
