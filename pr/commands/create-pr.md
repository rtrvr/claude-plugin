현재 브랜치에서 Pull Request를 생성해줘.

## Base Branch 선택
- **AskUserQuestion을 사용해서** base branch를 물어봐
- **첫 번째 옵션**: 분기 원점 브랜치 추론
  - `git merge-base <후보브랜치> HEAD` 결과로 각 브랜치와의 공통 조상 커밋 찾기
  - 해당 커밋이 후보 브랜치의 HEAD에 가장 가까운 브랜치를 분기 원점으로 추정
  - 추정된 브랜치를 "(분기 원점 추정, Recommended)"이라고 표시
- 두 번째 옵션: `develop`
- 나머지 옵션은 상황에 맞게 동적으로 제안 (예: main, master, 또는 관련 브랜치)
- 사용자가 선택한 브랜치를 base로 사용

## PR 제목 생성
- `git log <선택한-base-branch>..HEAD`로 현재 브랜치의 모든 커밋 확인
- 모든 커밋 메시지를 분석해서 **한국어로 요약된 제목** 생성
- 예: "feat(inventory): Refill 기능 개선 및 처방전 관리 추가"

## PR 본문 생성
1. **상단**: AI가 생성한 전체 변경사항 요약 (한국어, 2-3문장)
2. **하단**: 커밋들을 타입별로 그룹핑
   - `### Features` - feat로 시작하는 커밋들
   - `### Refactors` - refactor로 시작하는 커밋들
   - `### Fixes` - fix로 시작하는 커밋들
   - 기타 타입도 동일하게 처리

## 푸시 확인
- `git status`로 로컬에 푸시되지 않은 커밋이 있는지 확인
- "Your branch is ahead of" 메시지가 있으면 `git push` 실행
- 원격 브랜치가 없으면 `git push -u origin <branch-name>` 실행

## PR 생성 및 브라우저 열기
- `gh pr create --base <선택한-base-branch> --title "..." --body "..."`로 PR 생성
- PR 생성 후 `gh pr view --web` 명령으로 브라우저에서 PR 페이지 열기

## 주의사항
- 커밋이 없거나 이미 PR이 존재하면 안내 메시지 출력