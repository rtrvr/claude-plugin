---
allowed-tools: Bash(gh pr view:*),Bash(gh api:*)
description: Resolve PR review comments by fixing code
---

## Step 1: Get Current PR

현재 브랜치의 PR 정보를 가져옵니다:

```bash
gh pr view --json number,url
```

PR이 없으면 사용자에게 알리고 종료합니다.

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
' -f owner=pilly-io -f repo=pillo-android-v2 -F pr=PR_NUMBER
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

## Step 4: Fix Code Sequentially

선택된 comment들을 **순차적으로** 처리합니다:

각 comment에 대해:
1. 해당 파일과 라인을 `Read` 도구로 읽음
2. Comment의 피드백을 분석
3. 적절한 코드 수정을 `Edit` 도구로 적용
4. 수정 완료 후 다음 comment로 이동

**주의사항:**
- 코드 수정만 수행하고, thread resolve는 하지 않음
- 각 수정 후 사용자에게 수정 내용을 간략히 보고
- 불명확한 피드백은 사용자에게 확인 요청

## Step 5: Summary

모든 선택된 comment 처리 완료 후:

```
## 처리 완료

- 총 X개의 comment 처리
- 수정된 파일:
  - file1.kt (2개 comment)
  - file2.kt (1개 comment)

thread resolve는 수행하지 않았습니다.
PR에서 직접 확인 후 resolve해주세요.
```