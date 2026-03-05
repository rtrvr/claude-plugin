# Pillo BigQuery 스키마

프로젝트: `pillo-10a7d`
데이터셋: `analytics`
전체 경로 형식: `` `pillo-10a7d.analytics.<테이블명>` ``

---

## 테이블: events

앱에서 발생한 모든 이벤트 로그.

**파티션:** DAY (`event_timestamp`)
**클러스터링:** event_id, event_name, device_id

> **주의:** 쿼리 시 반드시 `WHERE event_timestamp >= TIMESTAMP('...')` 파티션 필터 포함. 없으면 전체 테이블 스캔으로 비용 폭증.

| 컬럼 | 타입 | 설명 |
|------|------|------|
| event_id | STRING | 이벤트 고유 ID (REQUIRED) |
| event_name | STRING | 이벤트 이름 (REQUIRED) |
| event_timestamp | TIMESTAMP | 이벤트 발생 시각 (REQUIRED, 파티션 키) |
| device_id | STRING | 디바이스 고유 ID (REQUIRED) |
| user_id | STRING | 로그인한 유저 ID (로그인 전 NULL) |
| session_id | STRING | 세션 ID |
| platform | STRING | 'ios' 또는 'android' |
| properties | JSON | 이벤트별 추가 속성 |
| app_version | STRING | 앱 버전 |
| os_version | STRING | OS 버전 |
| device_model | STRING | 기기 모델명 |
| device_manufacturer | STRING | 제조사 |
| carrier | STRING | 통신사 |
| country | STRING | 국가 코드 |
| city | STRING | 도시 |
| region | STRING | 지역 |
| language | STRING | 언어 설정 |
| timezone | STRING | 타임존 |
| airbridge_device_id | STRING | Airbridge 디바이스 ID |
| ip_address | STRING | IP 주소 |
| received_at | TIMESTAMP | 서버 수신 시각 |
| client_timestamp | TIMESTAMP | 클라이언트 발생 시각 |

**특수 이벤트:**
- `$identify` — 로그인/회원가입 시 발생. `properties['$set']`에 유저 속성 포함.

**properties 접근 패턴:**
```sql
JSON_VALUE(properties, '$.key_name')
```

---

## 테이블: identity_map

device_id ↔ user_id 매핑. 로그인 전(익명) → 로그인 후 동일 유저 추적용.

**클러스터링:** canonical_user_key
**갱신 주기:** 1시간마다 (from events의 $identify 이벤트)

| 컬럼 | 타입 | 설명 |
|------|------|------|
| device_id | STRING | 디바이스 ID (REQUIRED) |
| canonical_user_key | STRING | 통합 유저 키 — 로그인 전: device_id, 후: user_id |
| user_id | STRING | Firebase user ID (로그인 전 NULL) |
| first_seen_at | TIMESTAMP | 최초 발견 시각 |
| last_seen_at | TIMESTAMP | 최근 발견 시각 |
| user_id_set_at | TIMESTAMP | user_id 최초 설정 시각 (= 회원가입/로그인 시각으로 활용) |

---

## 테이블: user_profiles

유저별 집계 프로파일. identity_map 기반으로 1시간마다 갱신.

**클러스터링:** user_key
**갱신 주기:** 1시간마다

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

---

## 자주 쓰는 쿼리 패턴

### DAU (일별 활성 사용자)
```sql
SELECT
  DATE(event_timestamp) AS dt,
  COUNT(DISTINCT device_id) AS dau
FROM `pillo-10a7d.analytics.events`
WHERE event_timestamp >= TIMESTAMP_SUB(CURRENT_TIMESTAMP(), INTERVAL 30 DAY)
GROUP BY dt
ORDER BY dt
```

### 신규 가입자 수 (일별)
```sql
SELECT
  DATE(user_id_set_at) AS dt,
  COUNT(DISTINCT user_id) AS new_users
FROM `pillo-10a7d.analytics.identity_map`
WHERE user_id IS NOT NULL
  AND user_id_set_at >= TIMESTAMP_SUB(CURRENT_TIMESTAMP(), INTERVAL 30 DAY)
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

### 총 유저 수 (누적)
```sql
SELECT COUNT(DISTINCT user_id) AS total_users
FROM `pillo-10a7d.analytics.identity_map`
WHERE user_id IS NOT NULL
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

### 플랫폼별 DAU
```sql
SELECT
  DATE(event_timestamp) AS dt,
  platform,
  COUNT(DISTINCT device_id) AS dau
FROM `pillo-10a7d.analytics.events`
WHERE event_timestamp >= TIMESTAMP_SUB(CURRENT_TIMESTAMP(), INTERVAL 30 DAY)
GROUP BY dt, platform
ORDER BY dt, platform
```

---

## 데이터 흐름

```
앱 → Pulse Collector → Pub/Sub → GCS → events_external → events (1시간마다)
                                                          → identity_map (1시간마다)
                                                          → user_profiles (1시간마다)
```

- `events`: 1시간마다 GCS에서 로드 (최대 1시간 지연)
- `identity_map`, `user_profiles`: events 기반으로 1시간마다 갱신

## 주의사항

1. **파티션 필터 필수**: `events` 테이블 쿼리 시 `event_timestamp` 범위 조건 필수
2. **익명 유저 구분**: `user_id IS NULL` → 익명, `user_id IS NOT NULL` → 로그인 유저
3. **실시간 아님**: 모든 테이블은 최대 1시간 지연 있음
4. **properties는 JSON**: `JSON_VALUE(properties, '$.key')` 또는 `properties.key` 로 접근
