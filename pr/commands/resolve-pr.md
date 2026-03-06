---
allowed-tools: Bash(gh pr view:*),Bash(gh repo view:*),Bash(gh api:*)
description: Resolve PR review comments by fixing code
---

## Step 1: Get Current PR and Repository Info

현재 브랜치의 PR 정보와 레포지토리 정보를 가져옵니다:

```bash
gh pr view --json number,url
gh repo view --json owner,name
```

- PR이 없으면 사용자에게 알리고 종료합니다.
- `owner.login`과 `name` 값을 Step 2에서 사용합니다.

## Step 2: Fetch Unresolved Review Threads

GraphQL API로 미해결 review thread 목록을 가져옵니다:

```bash
gh api graphql -f query='
  query($owner: String!, $repo: String!, $pr: Int!) {
    repository(owner: $owner, name: $repo) {
      pullRequest(number: $pr) {
        reviewThreads(first: 100) {
          nodes {
            id
            isResolved
            path
            line
            comments(first: 10) {
              nodes {
                body
                author { login }
                createdAt
              }
            }
          }
        }
      }
    }
  }
' -f owner=OWNER -f repo=REPO -F pr=PR_NUMBER
```

`isResolved: false`인 thread만 필터링합니다.

## Step 3: Sort by Priority and Present Comments

### 3-1: 우선순위 파싱 및 정렬

Comment body에서 우선순위 태그를 파싱합니다:
- `[P1]` = 우선순위 1 (가장 높음, 버그/크리티컬)
- `[P2]` = 우선순위 2 (중요)
- `[P3]` = 우선순위 3 (개선 권장)
- `[P4]` = 우선순위 4 (낮음, 스타일/제안)
- 태그 없음 = 우선순위 5 (기본값)

**정렬 순서**: P1 → P2 → P3 → P4 → 태그 없음

### 3-2: 페이지네이션으로 선택지 제공

`AskUserQuestion`은 **최대 4개 옵션**만 지원합니다. 따라서:

1. 미해결 comment가 없으면 "해결할 comment가 없습니다" 메시지를 출력하고 종료
2. Comment를 우선순위순으로 정렬
3. **4개씩 나눠서** 사용자에게 제시

첫 번째 질문 예시 (5개 이상인 경우):
```
options:
  - label: "[P1] MainActivity.kt:42"
    description: "[P1] 버그: conditionIds에 잘못된 값 매핑..."
  - label: "[P2] RefillProvider.kt:274"
    description: "[P2] 무제한 while 루프로 인한 메모리 사용량..."
  - label: "[P3] ViewModel.kt:44"
    description: "[P3] DI 없이 인스턴스 직접 생성..."
  - label: "다음 페이지 보기"
    description: "나머지 N개의 comment 보기"
```

**"다음 페이지 보기"** 선택 시:
- 다음 3개 comment + "다음 페이지 보기" 옵션 제시
- 마지막 페이지면 "다음 페이지 보기" 옵션 없이 남은 comment만 제시

4개 이하인 경우:
- 페이지네이션 없이 모든 comment를 한 번에 제시
- **multiSelect: true**로 복수 선택 가능

## Step 4: Analyze and Fix Sequentially

선택된 comment들을 **순차적으로** 분석하고 처리합니다:

각 comment에 대해:

### 4-1: 코드 읽기

해당 파일과 라인을 `Read` 도구로 읽습니다. comment에서 언급하는 주변 코드까지 충분히 읽어 전체 맥락을 파악합니다.

### 4-2: Comment vs 실제 코드 분석

Comment의 주장과 실제 코드를 비교하여 3가지 중 하나로 판단합니다:

| 판단 | 기준 | 행동 |
|------|------|------|
| **동의** | comment가 맞고 수정 방향이 명확 | 수정안 제시 |
| **부분 동의** | 지적은 맞지만 제안된 방법이 최선이 아님 | 대안 제시 |
| **반박** | comment가 사실과 다르거나 이미 처리됨 | 근거와 함께 스킵 제안 |

### 4-3: 판단 결과 제시 및 사용자 확인

판단 결과를 사용자에게 보여주고 `AskUserQuestion`으로 확인합니다.

**동의 예시:**
```
## [P1] RefillProvider.kt:274
> 예외 발생 시 카운터가 증가하지 않아 무한 루프 가능

**판단: 동의**
L280의 catch 블록에서 retryCount++가 누락되어 있습니다.

수정안: catch 블록에 retryCount++ 추가

options:
  - "적용"
  - "직접 수정할게요"
  - "스킵"
```

**부분 동의 예시:**
```
## [P2] ViewModel.kt:44
> DI 없이 인스턴스 직접 생성하고 있음

**판단: 부분 동의**
직접 생성은 맞지만, 이 클래스는 테스트 전용 헬퍼라 DI가 과합니다.
대신 팩토리 메서드로 추출하면 테스트 용이성은 확보됩니다.

options:
  - "대안 적용 (팩토리 메서드)"
  - "원래 제안대로 (DI 적용)"
  - "스킵"
```

**반박 예시:**
```
## [P3] MainActivity.kt:42
> conditionIds에 잘못된 값이 매핑됨

**판단: 반박**
L38-45를 확인하면 conditionIds는 서버 응답의 `conditions` 필드에서
직접 매핑하며, L40의 filter로 null 값도 제거됩니다.
리뷰어가 L42의 map을 flatMap으로 착각한 것으로 보입니다.

options:
  - "그래도 수정"
  - "스킵"
```

### 4-4: 사용자 선택에 따라 수정 또는 스킵

- **적용/대안 적용**: `Edit` 도구로 코드 수정
- **직접 수정할게요**: 해당 comment를 스킵하고 다음으로 이동
- **스킵**: 수정하지 않고 다음 comment로 이동

**주의사항:**
- 코드 수정만 수행하고, thread resolve는 하지 않음
- 불명확한 피드백은 사용자에게 추가 확인 요청

## Step 5: Summary

모든 선택된 comment 처리 완료 후:

```
## 처리 완료

- 총 X개의 comment 처리
  - 수정: N개
  - 스킵: N개

- 수정된 파일:
  - file1.kt (2개 comment)
  - file2.kt (1개 comment)

- 스킵 사유:
  - MainActivity.kt:42 — 반박: conditionIds 매핑은 정상 동작 (L38-45 참고)
  - ViewModel.kt:44 — 사용자 직접 수정 예정

thread resolve는 수행하지 않았습니다.
PR에서 직접 확인 후 resolve해주세요.
```

**스킵 사유**는 PR에서 리뷰어에게 답글 달 때 참고할 수 있도록 근거를 포함합니다.