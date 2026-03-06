---
name: metabase
description: Pillo BigQuery 데이터를 Metabase에서 분석하는 스킬. MCP가 연결된 경우 쿼리를 직접 실행하고 대시보드를 생성/조회한다. MCP 미연결 시 BigQuery SQL을 생성해 붙여넣기 안내한다. "DAU 뽑아줘", "신규 가입자 추이", "대시보드 만들어줘", "어떤 대시보드 있어", "차트 어떻게 만들어" 등의 요청 시 사용한다.
---

# Metabase — Pillo 데이터 분석

## Overview

Pillo 앱 데이터를 Metabase에서 분석하는 스킬. 두 가지 모드로 동작한다:

- **MCP 모드** (권장): `metabase` MCP 서버가 연결된 경우. Claude가 직접 쿼리 실행, 대시보드 조회/생성 가능.
- **SQL 생성 모드** (폴백): MCP 미연결 시. BigQuery SQL을 생성해 Metabase Native Query에 붙여넣기 안내.

MCP 미설정 시 `/setup-metabase-mcp` 실행을 안내한다.

스키마 상세는 `references/schema.md`, 이벤트/유저 프로퍼티 목록은 `references/taxonomy.md` 참조.

---

## MCP 모드 워크플로우

> `metabase` MCP 도구가 사용 가능한 경우 이 워크플로우를 따른다.

### Step 1: 의도 분류

| 요청 유형 | 수행할 작업 |
|----------|------------|
| 데이터 조회 ("DAU 보여줘", "가입자 수") | SQL 작성 후 직접 실행 |
| 저장된 질문 실행 ("리텐션 질문 실행해줘") | 질문 ID로 실행 |
| 대시보드/컬렉션 탐색 | 목록 조회 후 상세 확인 |
| 검색 ("복약 관련 대시보드 찾아줘") | 키워드로 전체 검색 |
| 대시보드 생성 | 대시보드 생성 후 카드 추가 |

### Step 2: 쿼리 실행

1. `references/schema.md`로 관련 테이블/컬럼 파악
2. `references/taxonomy.md`로 이벤트명/프로퍼티 키 확인
3. SQL 작성 후 직접 실행
4. 결과를 한국어로 해석해서 설명

**SQL 작성 규칙:**
- `events` 테이블은 반드시 `WHERE event_timestamp >= TIMESTAMP('...')` 파티션 필터 포함
- 기간 미명시 시 최근 30일 기본 적용
- `properties` 접근: `JSON_VALUE(properties, '$.key')` 패턴
- 쿼리 실행 전 Database ID 먼저 확인

### Step 3: 대시보드 생성 워크플로우

1. 컬렉션 생성 (필요시)
2. 분석 쿼리 테스트 실행
3. 대시보드 생성
4. 카드 추가

---

## SQL 생성 모드 워크플로우 (MCP 폴백)

> MCP 미연결 시 이 워크플로우를 따른다.
> 세션 시작 시 안내: "MCP를 연결하면 직접 쿼리를 실행할 수 있습니다. `/setup-metabase-mcp`를 실행해보세요."

### Step 1: 의도 분류

**SQL 생성** — 키워드: "몇 명", "얼마나", "몇 건", "집계", "쿼리", "뽑아", "DAU", "MAU", "가입자", "이벤트 수", "리텐션"

**시각화 가이드** — 키워드: "차트", "그래프", "대시보드", "시각화", "어떻게 보여", "어떻게 만들어"

**모호한 경우**: SQL 생성 후 시각화 연결 제안.

### Step 2: 스키마 파악

`references/schema.md`와 `references/taxonomy.md`를 읽어 관련 테이블/컬럼/이벤트명 파악.

### Step 3-A: SQL 생성 응답

```
## [질문 요약] 쿼리

**사용 테이블:** [테이블명]

​```sql
[완성된 BigQuery SQL]
​```

**Metabase 실행 방법:**
1. Metabase 접속 → "+ New" → "SQL query"
2. "BigQuery - pillo-10a7d" 선택
3. 위 쿼리 붙여넣고 "Run" (▶) 클릭
```

**SQL 작성 규칙:**
- `events` 테이블: `WHERE event_timestamp >= TIMESTAMP('...')` 파티션 필터 필수
- 기간 미명시 시 최근 30일 기본
- `properties` 접근: `JSON_VALUE(properties, '$.key')` 패턴

### Step 3-B: 시각화 가이드

```
## [질문 요약] 시각화 방법

**추천 차트:** [타입] — [이유 1줄]

**Metabase 설정:**
1. [단계별 UI 안내]
```

**차트 타입 선택:**
- 시간별 추이 → Line Chart
- 카테고리 비교 → Bar Chart
- 단일 핵심 수치 → Number (KPI)
- 상세 데이터 → Table
- 비율/구성 (5개 이하) → Pie Chart

---

## 오류 처리

| 상황 | 응답 |
|------|------|
| MCP 연결 안 됨 | "MCP가 연결되지 않았습니다. `/setup-metabase-mcp`로 설정 후 다시 시도해주세요. 지금은 SQL 생성 모드로 진행합니다." |
| 테이블/컬럼 불확실 | "어떤 데이터를 보고 싶으신지 더 구체적으로 알려주시면 정확한 쿼리를 드릴 수 있어요." |
| 이벤트명 불확실 | "`references/taxonomy.md`에서 이벤트 목록을 확인하거나, MCP로 `SELECT DISTINCT event_name` 직접 조회 가능합니다." |
| 실시간 데이터 요청 | "identity_map과 user_profiles는 1시간마다 갱신됩니다. 실시간은 events 테이블을 직접 조회하세요." |

## References

- `references/schema.md` — BigQuery 테이블 스키마 및 쿼리 패턴
- `references/taxonomy.md` — 이벤트명(124개) 및 유저 프로퍼티(91개) 정의
