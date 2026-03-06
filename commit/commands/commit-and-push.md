---
description: tracked 파일의 변경사항을 커밋하고 push까지 수행합니다.
allowed-tools: Bash, Read, AskUserQuestion
argument-hint: (없음)
---

`git status`에서 **tracked files의 변경사항만** 모두 커밋하고, push까지 해줘 (하나의 커밋으로). 커밋 내용은 한국어로 적어줘.

- `git add .`를 사용하지 마 (untracked files를 포함시킬 수 있음)
- `git add -u` 또는 각 파일을 명시적으로 add해
- Untracked files (`??`로 표시)는 무시해
- 커밋 전에 git diff로 확인하고, 의심스러운 내용(테스트 로그, 테스트용 코드)이 있으면 물어봐
- 커밋 후 현재 브랜치에 `git push`해. upstream이 설정되지 않았으면 `git push -u origin <branch>`를 사용해
- push 실패 시 원인을 알려주고 사용자에게 확인을 받아