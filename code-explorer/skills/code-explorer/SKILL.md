---
name: code-explorer
description: pillo 프로젝트(Android/iOS) GitHub private repo를 로컬에 clone/pull하고, 비개발자가 코드에 대해 자유 질문할 수 있는 스킬. "코드 보여줘", "이 기능 어떻게 동작해?", "버그 원인 찾아줘", "show me the code", "how does this work?", "find the bug", "explain this feature", "project structure" 등의 요청 시 이 스킬을 사용한다.
---

# Code Explorer — pillo 프로젝트 코드 탐색

## Overview

비개발자가 pillo 프로젝트의 Android/iOS 코드를 쉽게 탐색하고, 자유롭게 질문할 수 있도록 돕는 스킬. 코드를 **읽기 전용**으로 탐색하며, 수정은 하지 않는다.

## Workflow

### Step 0: GitHub 인증 확인

아래 순서대로 의존성을 확인한다. 하나라도 실패하면 해당 안내를 출력하고 **중단**한다.

**1) `which brew` — Homebrew 확인**

실패 시:
```
Homebrew가 설치되어 있지 않습니다. 터미널에서 아래 명령어를 붙여넣고 실행해주세요:

/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

설치 완료 후 다시 /explore-code 를 실행해주세요.
```

**2) `which git` — Git 확인**

실패 시:
```
Git이 설치되어 있지 않습니다. 터미널에서 아래 명령어를 실행해주세요:

brew install git

설치 완료 후 다시 /explore-code 를 실행해주세요.
```

**3) `which gh` — GitHub CLI 확인**

실패 시:
```
GitHub CLI가 설치되어 있지 않습니다. 터미널에서 아래 명령어를 실행해주세요:

brew install gh

설치 완료 후 다시 /explore-code 를 실행해주세요.
```

**4) `gh auth status` — GitHub 인증 확인**

실패 시:
```
GitHub 로그인이 필요합니다. 터미널에서 아래 명령어를 실행해주세요:

1. `gh auth login` 실행
2. "GitHub.com" 선택
3. "HTTPS" 선택
4. 브라우저에서 로그인 완료

완료되면 다시 /explore-code 를 실행해주세요.
```

모두 통과하면 Step 1로 진행한다.

### Step 1: 저장소 선택

`repo-config.md`에서 저장소 목록을 읽는다.

`$ARGUMENTS`에서 저장소 힌트를 탐지한다:
- "android", "안드로이드" → Android 저장소 선택
- "ios", "아이폰" → iOS 저장소 선택
- 힌트가 없거나 모호하면 → `AskUserQuestion`으로 선택 요청

```
어떤 프로젝트의 코드를 살펴볼까요?
- Android (pillo-android-v2)
- iOS (pillo-ios)
```

### Step 2: 코드 가져오기 (Clone 또는 Pull)

선택된 저장소의 로컬 경로를 확인한다.

**로컬 경로가 없는 경우 (첫 사용)**:
1. `mkdir -p ~/pillo` 실행
2. `git clone <repo-url> <로컬-경로>` 실행
3. 완료 메시지: "코드를 가져왔습니다! 질문이 있으시면 말씀해주세요."

**로컬 경로가 있는 경우 (이후 사용)**:
1. `git -C <로컬-경로> status --porcelain`으로 uncommitted changes 확인
2. 변경 사항이 있으면 `git -C <로컬-경로> stash`로 임시 저장 후 pull
3. `git -C <로컬-경로> pull` 실행
4. 완료 메시지: "최신 코드로 업데이트했습니다!"

### Step 3: 질문 응답

`$ARGUMENTS`에 질문이 포함되어 있으면 바로 응답한다. 없으면 "어떤 것이 궁금하신가요?"로 대기한다.

**응답 원칙**:
- 전문 용어에는 짧은 부연 설명을 붙인다
- 코드 파일을 인용할 때는 파일명과 간단한 설명을 함께 제시
- 코드 블록은 최소화하고, 꼭 필요한 경우만 짧게 포함

**질문 유형별 응답 가이드**:

#### 1. 코드 이해 ("이게 뭐하는 코드야?", "이 기능 어떻게 동작해?")
- 해당 코드를 Glob/Grep/Read로 찾아서 읽는다
- 핵심 흐름을 3-5단계로 정리

#### 2. 버그 리포트 ("이런 문제가 있어", "왜 이렇게 되는 거야?")
- 관련 코드를 탐색하여 원인 후보를 정리
- 구조화된 요약 제공:
  - **증상**: 사용자가 보고한 문제
  - **추정 원인**: 코드에서 확인된 부분
  - **관련 파일**: 파일명과 위치

#### 3. 기능 요청 ("이런 기능 추가하려면?", "이거 바꿀 수 있어?")
- 변경이 필요한 파일과 범위를 정리
- 난이도를 간단/보통/복잡으로 표시
- "이 내용을 개발자에게 전달하시면 됩니다" 형태로 마무리

#### 4. 구조/개요 ("전체 구조 알려줘", "어떤 파일이 뭘 하는 거야?")
- 계층을 구분하여 역할 설명
- 주요 폴더와 역할을 테이블로 정리

#### 5. 변경 이력 ("최근에 뭐가 바뀌었어?", "이 파일 언제 수정됐어?")
- `git -C <로컬-경로> log --oneline -20` 등으로 이력 조회
- 날짜별로 요약하여 설명

## 오류 처리

| 상황 | 메시지 |
|------|--------|
| `brew` 미설치 | "Homebrew가 설치되어 있지 않습니다. 설치 명령어를 안내합니다." |
| `gh` 미설치 | "GitHub CLI가 설치되어 있지 않습니다. `brew install gh`를 터미널에서 실행해주세요." |
| `gh` 인증 안 됨 | "`gh auth login` 실행 → GitHub.com → HTTPS → 브라우저 로그인 후 다시 시도해주세요." |
| `git` 미설치 | "Git이 설치되어 있지 않습니다. `brew install git`을 터미널에서 실행해주세요." |
| 권한 없음 (clone 실패) | "이 저장소에 접근 권한이 없습니다. 관리자에게 GitHub 초대를 요청해주세요." |
| 네트워크 오류 | "인터넷 연결을 확인해주세요. 연결 후 다시 시도해주세요." |
| dirty working tree (pull 시) | uncommitted changes를 `git stash`로 임시 저장 후 pull 재시도 |
| merge conflict (pull 시) | "코드 업데이트 중 충돌이 발생했습니다. 개발자에게 문의해주세요." |

## References

저장소 목록과 경로 설정은 `repo-config.md`를 참조한다. 새 저장소를 추가하려면 해당 파일에 행을 추가하면 된다.
