---
name: metabase
description: Pillo 앱의 BigQuery 데이터를 Metabase에서 분석하기 위한 SQL 쿼리 생성 및 시각화 가이드 스킬. 비개발자가 자연어로 데이터 질문을 하면 Metabase Native Query에 바로 붙여넣을 수 있는 BigQuery SQL을 생성하거나 차트/대시보드 설정 방법을 안내한다. "DAU 뽑아줘", "신규 가입자 추이", "이벤트 몇 건이야", "차트 어떻게 만들어", "대시보드 설정 방법" 등의 요청 시 사용한다.
---

# Metabase — Pillo 데이터 분석 가이드

## Overview

비개발자가 Metabase에서 Pillo 앱 데이터를 분석할 수 있도록 SQL 쿼리를 생성하거나 시각화 방법을 안내한다.
스키마 상세는 `references/schema.md`를 참조한다.

## Workflow

### Step 1: 의도 분류

`$ARGUMENTS`를 읽어 분류한다.

**SQL 생성** — 다음 키워드 포함 시:
- "몇 명", "얼마나", "몇 건", "몇 번", "집계", "쿼리", "뽑아", "조회", "DAU", "MAU", "가입자", "이벤트 수", "리텐션"

**시각화 가이드** — 다음 키워드 포함 시:
- "차트", "그래프", "대시보드", "시각화", "어떻게 보여", "어떻게 만들어"

**모호한 경우**: SQL 생성 후 시각화 연결 제안.

### Step 2: 스키마 파악

`references/schema.md`를 읽어 관련 테이블/컬럼을 파악한다.

| 질문 유형 | 참조 테이블 |
|----------|------------|
| 이벤트/행동 분석 | `events` |
| 유저 수, 신규 가입자 | `identity_map` |
| 유저 속성/프로파일 | `user_profiles` |

### Step 3-A: SQL 생성 응답 형식

```
## [질문 요약] 쿼리

**사용 테이블:** [테이블명]
**기간:** [적용된 기간]

​```sql
[완성된 BigQuery SQL]
​```

**Metabase 실행 방법:**
1. Metabase 접속 → 상단 "+ New" 클릭
2. "SQL query" 선택
3. 데이터베이스로 "BigQuery - pillo-10a7d" 선택
4. 위 쿼리 붙여넣고 "Run" (▶) 클릭

**결과 설명:** [1-2줄]
```

**SQL 작성 규칙:**
- `events` 테이블은 반드시 `WHERE event_timestamp >= TIMESTAMP('...')` 파티션 필터 포함
- 기간 미명시 시 최근 30일 기본 적용
- user_id 기준 조회 시 `identity_map` JOIN 사용
- `properties` 접근: `JSON_VALUE(properties, '$.key')` 패턴

### Step 3-B: 시각화 가이드 응답 형식

```
## [질문 요약] 시각화 방법

**추천 차트:** [타입] — [이유 1줄]

**Metabase 설정:**
1. [단계별 UI 안내]
2. ...

**기반 쿼리 (필요시):**
[SQL]
```

**차트 타입 선택:**
- 시간별 추이 → Line Chart
- 카테고리 비교 → Bar Chart
- 단일 핵심 수치 → Number (KPI)
- 상세 데이터 → Table
- 비율/구성 (5개 이하) → Pie Chart

## 오류 처리

| 상황 | 응답 |
|------|------|
| 테이블/컬럼 불확실 | "어떤 데이터를 보고 싶으신지 조금 더 구체적으로 알려주시면 정확한 쿼리를 드릴 수 있어요." |
| 이벤트명 불확실 | "Metabase에서 `SELECT DISTINCT event_name FROM ...` 으로 목록을 먼저 확인해보세요." |
| 실시간 데이터 요청 | "identity_map과 user_profiles는 1시간마다 갱신됩니다. 실시간은 events 테이블을 직접 조회하세요." |

## References

- `references/schema.md` — BigQuery 테이블 스키마 상세 및 자주 쓰는 쿼리 패턴
- `references/taxonomy.md` — 이벤트명 목록 및 properties 키 정의 (추후 추가 예정)
