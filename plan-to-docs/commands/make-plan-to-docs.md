이 계획을 .docs 디렉토리 아래에 폴더 구조로 생성해주세요.
폴더 이름은 내용에 맞게 적절히 지정하세요. (예: feature-name-YYYYMMDD/)

**(매우 중요!) 문서를 작성하면서 더 좋은 계획이 있다면, 그 계획으로 작성하세요.**

문서는 한국어로 작성해야 합니다.

---

## Step 1: 문서 수준 선택

**먼저 AskUserQuestion을 사용해 사용자에게 문서 수준을 물어보세요.**

### 자동 추천 기준
- **간단 추천**: (1-3 Phase) AND (수정 파일 5개 미만)
- **상세 추천**: 그 외

### 질문 형식
```
header: "문서 수준"
question: "이 계획은 [X Phase, Y개 파일]입니다. 어떤 수준의 문서를 생성할까요?"
options:
  1. "간단 (1파일)" - "(추천)" 표시 조건에 맞으면 추가
  2. "상세 (전체 구조)" - "(추천)" 표시 조건에 맞으면 추가
```

---

## Step 2: 선택에 따라 템플릿 적용

- **"간단" 선택 시** → 아래 "간단 문서 템플릿" 사용
- **"상세" 선택 시** → 아래 "상세 문서 템플릿" 사용

---

# 간단 문서 템플릿

**1개 파일만 생성**: `.docs/[feature-name-YYYYMMDD]/plan.md`

```markdown
# [Feature Name]

> 한 줄 설명

---

## Progress Status

| Phase | Status | Completed | Notes |
|-------|--------|-----------|-------|
| Phase 1: [Name] | ⬜ Not Started | - | - |
| Phase 2: [Name] | ⬜ Not Started | - | - |

**Status 값**: ⬜ Not Started / 🟡 In Progress / ✅ Done / ⚠️ Blocked

---

## Summary
[2-3 문장으로 전체 요약 - 이 작업이 무엇이고, 왜 필요하며, 어떻게 해결하는지]

---

## Goal
- 이 작업이 달성하려는 것 (2-3 문장)

## Non-Goals ⚠️
- 이 작업에서 **의도적으로 제외**하는 것

---

## Spec

### Feature Description
완성된 기능을 최종 사용자 관점에서 설명 (1-2 paragraphs)

### Success Criteria
- [ ] 완료 조건 1
- [ ] 완료 조건 2

### Acceptance Tests
| Test Case | Expected Result | Priority |
|-----------|-----------------|----------|
| 사용자가 X를 하면 | Y가 발생한다 | High |

---

## Technical Plan

### Approach
구현 접근 방식 (1-2 paragraphs)

### Risks & Constraints
- 알려진 위험/제약 사항

### AI Context Block
```
**Key Constraints**:
- [프로젝트 특정 제약사항]

**Related Files**:
- `path/to/file.kt` - 수정 대상

**Non-Goals (AI가 하면 안 되는 것)**:
- [명시적으로 하지 말아야 할 것]
```

---

## Tasks

### Phase 1: [Phase Name]
- [ ] Task 1
- [ ] Task 2

### Phase 2: [Phase Name] (필요시)
- [ ] Task 3
- [ ] Task 4

---

## Context

| File | Purpose | Priority |
|------|---------|----------|
| `path/to/file.kt` | 설명 | High/Low |

---

## Progress Log
- YYYY-MM-DD: 시작
```

---

# 상세 문서 템플릿

**전체 폴더 구조 생성**:

```
.docs/[feature-name-YYYYMMDD]/
├── README.md              # Entry point: 진행 상황 + 전체 요약
├── 01-overview.md         # Goals, Non-Goals, Background
├── 02-spec.md             # Specification, Acceptance Tests
├── 03-technical-plan.md   # 기술 계획, AI Context Block, Constitution Check
├── 04-context.md          # 관련 파일 목록 (AI 참조용)
├── 05-decisions.md        # Key Decisions, Trade-offs
├── phases/
│   ├── phase-0.md         # Phase 0: 준비/조사
│   ├── phase-1.md         # Phase 1: 핵심 구현
│   └── phase-N.md         # 필요한 만큼 추가
├── verification.md        # 검증 방법, 테스트
└── progress-log.md        # 진행 로그 (append-only)
```

---

## File Templates (상세):

### README.md (Entry Point)
```markdown
# [Feature Name]

> 한 줄 설명

## Progress Status

| Phase | Status | Completed | Notes |
|-------|--------|-----------|-------|
| Phase 0: 준비 | ⬜ Not Started | - | - |
| Phase 1: 핵심 구현 | ⬜ Not Started | - | - |

## Quick Links

- [Overview](./01-overview.md) - 목표, 배경
- [Spec](./02-spec.md) - 스펙, 수락 테스트
- [Technical Plan](./03-technical-plan.md) - 기술 계획
- [Context](./04-context.md) - 관련 파일
- [Decisions](./05-decisions.md) - 주요 결정
- [Verification](./verification.md) - 검증 방법
- [Progress Log](./progress-log.md) - 진행 기록

## Phases

- [Phase 0: 준비](./phases/phase-0.md)
- [Phase 1: 핵심 구현](./phases/phase-1.md)

## Summary

[2-3 문장으로 전체 요약]
```

---

### 01-overview.md
```markdown
# Overview

## Goals
- 이 작업이 달성하려는 것 (2-3 문장)
- 성공의 정의

## Non-Goals ⚠️
- 이 작업에서 **의도적으로 제외**하는 것
- 미래로 미루는 것
- 범위 밖인 것

> Non-Goals는 scope creep 방지에 필수입니다.

## Background
- 이 작업이 필요한 이유
- 관련 이슈/문서 (있다면)
```

---

### 02-spec.md
```markdown
# Specification

## Feature Description
완성된 기능을 최종 사용자 관점에서 설명 (1-3 paragraphs)

## Success Criteria
- [ ] 완료 조건 1
- [ ] 완료 조건 2

## Acceptance Tests
| Test Case | Expected Result | Priority |
|-----------|-----------------|----------|
| 사용자가 X를 하면 | Y가 발생한다 | High |
```

---

### 03-technical-plan.md
```markdown
# Technical Plan

## Approach
구현 접근 방식과 주요 결정 (1-3 paragraphs)

## Alternatives Considered
| Option | Pros | Cons | Decision |
|--------|------|------|----------|
| Option A | ... | ... | ✅ Selected |
| Option B | ... | ... | ❌ Rejected |

## Risks & Constraints
- 알려진 위험
- 제약 사항

## Dependencies
- 라이브러리, API, 시스템 요구사항, 버전 제약

---

## AI Context Block

> AI가 이 문서 작업 시 참조할 필수 정보

```
**Project Type**: Android App (Kotlin, Jetpack Compose)
**Architecture**: MVVM + Clean Architecture
**Key Constraints**:
- Minimum SDK: 26
- Error handling only in ViewModel
- CancellationException must be rethrown

**Related Files** (AI가 반드시 읽어야 할 파일):
- `app/src/.../ViewModel.kt` - 수정 대상
- `domain/.../UseCase.kt` - 참조용
- `CLAUDE.md` - 프로젝트 규칙

**Explicit Non-Goals** (AI가 하면 안 되는 것):
- 기존 XML 레이아웃을 Compose로 마이그레이션 금지
- 이 작업에서 테스트 커버리지 개선 금지
- 관련 없는 코드 리팩토링 금지
```

---

## Constitution Check

Phase 시작 전/후 검증:

| Principle | Status | Justification |
|-----------|--------|---------------|
| Simplicity Over Cleverness | ⬜ | - |
| Error Handling in ViewModel Only | ⬜ | - |
| New Screen = Compose + M3 | ⬜ | - |
| No Unnecessary Abstraction | ⬜ | - |

**Complexity Tracking** (원칙 위반 시에만 작성):

| Violation | Why Essential | Alternatives Rejected |
|-----------|---------------|----------------------|
| - | - | - |
```

---

### 04-context.md
```markdown
# Context

> 관련 파일과 그 역할. AI가 작업 시 이 파일을 먼저 읽어야 함.

## Files to Modify
| File | Purpose | Priority |
|------|---------|----------|
| `path/to/file.kt` | 설명 | High |

## Files to Reference
| File | Purpose |
|------|---------|
| `path/to/reference.kt` | 패턴 참조용 |

## Existing Patterns to Follow
- 기존 아키텍처/패턴 설명
- 따라야 할 컨벤션

## Key Interfaces/Classes
```kotlin
// 주요 인터페이스/클래스 코드 스니펫 (필요시)
```
```

---

### 05-decisions.md
```markdown
# Key Decisions

## Decision Log

### [Decision 1 Title]
- **Date**: YYYY-MM-DD
- **Decision**: 결정 내용
- **Rationale**: 이유
- **Trade-offs**: 감수한 트레이드오프
- **Alternatives Rejected**: 거부한 대안과 이유

## What We Decided NOT to Do
- 하지 않기로 한 것과 그 이유
```

---

### phases/phase-N.md (Phase별 템플릿)
```markdown
# Phase N: [Phase Name]

**Status**: ⬜ Not Started / 🟡 In Progress / ✅ Done
**Estimated**: Xh
**Actual**: -

## Objective
이 Phase에서 달성할 것

## Prerequisites
- Phase N-1 완료
- 필요한 사전 조건

---

## Tasks

- [ ] Task 1 **(Est: Xh)**
- [ ] Task 2 **(Est: Xh)**
- [ ] Task 3 **(Est: Xh)**

---

## Implementation Notes

> 구현하면서 발견한 것들 기록

---

## Phase Verification

완료 후 확인:

```
Please review the Phase N implementation:
1. Constitution principle compliance
2. Non-Goals violation check
3. Error handling pattern verification
4. Testability evaluation
```

**Commit Point**: Phase N 완료 후 커밋

---

## README 업데이트

Phase 완료 시 반드시 README.md의 Progress Status 테이블 업데이트:

```markdown
| Phase N: [Name] | ✅ Done | YYYY-MM-DD | 완료 노트 |
```

Status 값:
- ⬜ Not Started
- 🟡 In Progress
- ✅ Done
- ⚠️ Blocked
```

---

### verification.md
```markdown
# Verification

## Test Commands
```bash
# 빌드 확인
./gradlew :app:compileDebugKotlin

# 테스트 실행
./gradlew test

# Maestro 테스트 (있다면)
maestro test flows/feature-test.yaml
```

## Manual Checks
- [ ] 체크 항목 1
- [ ] 체크 항목 2

## Regression Tests
기존 기능 중 확인해야 할 것:
- [ ] 기존 기능 A가 여전히 동작
- [ ] 기존 기능 B가 여전히 동작

## Performance Checklist
- [ ] 메모리 누수 없음
- [ ] ANR 없음
- [ ] 배터리 사용량 적정
```

---

### progress-log.md
```markdown
# Progress Log

> 작업하면서 계속 추가 (append-only)

---

## [YYYY-MM-DD]

### Done
- 완료한 작업

### Findings
- 발견한 것, 예상과 다른 점

### Blockers
- 막힌 점과 해결 방법

### Next
- 다음에 할 것

---
```

---

## Requirements:

1. **Step 1 필수**: 반드시 AskUserQuestion으로 문서 수준 선택
2. **선택에 따라 구조 결정**: 간단(1파일) 또는 상세(전체 구조)
3. **README.md가 허브** (상세 선택 시): 전체 진행 상황을 한눈에 파악
4. **Phase별 분리** (상세 선택 시): 각 Phase는 독립 파일로 작업
5. **Context 분리**: AI가 자주 참조하는 파일 목록은 별도 관리
6. **Progress Log**: append-only로 지속 업데이트
7. **Phase 완료 시 README 업데이트**: Progress Status 테이블 즉시 갱신
8. **6개월 후 재개 가능**: 문서만 읽고 작업 재개 가능해야 함

## Implementation Guidelines:
- UI 텍스트, 버튼 라벨, 메시지 등 모든 문자열 리소스에 적용

--ultrathink
