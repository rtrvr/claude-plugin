`git status`에서 **tracked files의 변경사항만** 모두 커밋해줘 (하나의 커밋으로). 커밋 내용은 한국어로 적어줘.

- `git add .`를 사용하지 마 (untracked files를 포함시킬 수 있음)
- `git add -u` 또는 각 파일을 명시적으로 add해
- Untracked files (`??`로 표시)는 무시해
- 커밋 전에 git diff로 확인하고, 의심스러운 내용(테스트 로그, 테스트용 코드)이 있으면 물어봐