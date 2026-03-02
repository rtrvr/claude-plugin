---
description: MISTAKES.md에 새 실수 패턴을 추가합니다.
argument-hint: <카테고리> <제목>
allowed-tools: Read, Edit, Grep, AskUserQuestion
---

# Add Mistake

`.claude/MISTAKES.md`에 새 실수 패턴을 추가합니다.

**입력**: $ARGUMENTS

---

## Step 1: 입력 파싱

`$ARGUMENTS`에서 카테고리와 제목/설명을 추출하세요.

- **카테고리**: `assumption`, `scope`, `timing`, `lifecycle` 중 하나
- **제목**: 실수 패턴의 핵심을 요약하는 짧은 문구

### 입력이 없거나 불충분한 경우

**대화 맥락에서 추론을 먼저 시도**:

1. 지금까지의 대화에서 다음을 추출:
   - 잘못된 가정이나 접근 방식은 무엇이었는가?
   - 올바른 답/접근은 무엇이었는가?
   - 어떤 카테고리에 해당하는가?
2. 추론 결과를 `AskUserQuestion`으로 사용자에게 확인:
   - "대화에서 다음 실수 패턴을 발견했습니다: [추론 내용]. 맞습니까?"
   - 카테고리/제목/설명 수정 기회 제공

대화에서도 추론이 불가능하면 `AskUserQuestion`으로 직접 질문:
- 어떤 카테고리에 해당하는가?
- 실수의 핵심은 무엇인가?
- 어떤 상황에서 발생했는가?

---

## Step 2: 중복 확인

`.claude/MISTAKES.md`에서 유사한 항목이 있는지 Grep으로 검색.

```
Grep: 제목의 핵심 키워드로 검색
```

유사 항목이 있으면 사용자에게 알리고 계속할지 확인.

---

## Step 3: 항목 구성

형식:
```
- **제목** [domain-tag] -- 설명. (출처)
```

- **domain-tag**: 기술 도메인 (예: `android-ui`, `room-sql`, `coroutines`, `hilt`, `analysis`)
- **설명**: 무엇이 잘못되었고, 올바른 접근은 무엇인지
- **출처**: 발견된 맥락 (예: `rethink`, `debugging`, 이슈 번호, 기능명)

---

## Step 4: MISTAKES.md에 추가

1. `.claude/MISTAKES.md` 읽기
2. 해당 카테고리 섹션의 마지막 항목 뒤에 새 항목 추가 (Edit 사용)
3. 파일 하단 메타데이터 갱신:
   - `마지막 업데이트` → 오늘 날짜
   - `항목 수` → +1 증가

---

## Step 5: 확인

추가 결과를 사용자에게 출력:

```
✅ MISTAKES.md에 추가됨
   카테고리: [카테고리]
   항목: **제목** [tag] -- 설명
   총 항목 수: N
```
