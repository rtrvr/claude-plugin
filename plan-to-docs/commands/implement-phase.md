$ARGUMENTS 에 해당하는 Phase를 구현해주세요.

**중요**: 각 단계를 건너뛰지 말고 순서대로 진행하세요. 컨텍스트 수집 없이 바로 구현을 시작하지 마세요.

---

## 절차

### 0. 문서 구조 파악

**MUST DO**: 먼저 문서 구조가 상세/간단 중 어느 것인지 확인:

```bash
# 상세 구조인지 확인
ls .docs/[folder]/phases/
ls .docs/[folder]/04-context.md

# 간단 구조인지 확인
ls .docs/[folder]/plan.md
```

- **상세 구조**: `phases/`, `04-context.md` 등 여러 파일 존재 → 아래 절차 모두 수행
- **간단 구조**: `plan.md` 하나만 존재 → Step 2~5는 plan.md에서 해당 섹션 읽기

---

### 1. Phase 파일 찾기 및 기본 정보 확인

**MUST DO**:
1. `.docs/` 디렉토리에서 대상 폴더 확인 (보통 가장 최근 또는 $ARGUMENTS로 지정)
2. Phase 파일 읽기:
   - 상세: `phases/phase-N.md`
   - 간단: `plan.md`의 해당 Phase 섹션
3. **Status 확인**:
   - `⬜ Not Started` 또는 `🟡 In Progress`인지 확인
   - 이미 `✅ Done`이면 사용자에게 알리고 중단

---

### 2. Context 수집 (MANDATORY - 건너뛰지 말 것)

**다음 순서대로 반드시 모두 읽으세요**:

#### 상세 구조의 경우:
1. **README.md** - 전체 진행 상황, Summary
2. **04-context.md** - 관련 파일 목록
   - **CRITICAL**: "Files to Modify", "Files to Reference"에 나열된 **모든 파일을 실제로 읽기**
   - 이 파일들을 읽지 않고 구현하면 안 됨
3. **03-technical-plan.md** - AI Context Block (제약사항), Constitution Check
4. **01-overview.md** - Goals, **Non-Goals** (절대 하지 말아야 할 것)
5. **02-spec.md** - 스펙, Success Criteria

#### 간단 구조의 경우:
1. **plan.md** 전체 읽기
2. **Context 섹션의 모든 파일 실제로 읽기**
3. **AI Context Block** 확인 (Technical Plan 섹션)
4. **Non-Goals** 확인 (Goal 섹션)

---

### 3. 구현 전 체크리스트

**구현 시작 전 반드시 확인**:

- [ ] 04-context.md (또는 plan.md Context)에 나온 모든 파일을 읽었는가?
- [ ] Non-Goals를 확인했는가? (하지 말아야 할 것)
- [ ] AI Context Block의 제약사항을 확인했는가?
- [ ] Prerequisites가 충족되었는가? (이전 Phase 완료 등)

**체크리스트 통과 후 다음 단계 진행**

---

### 4. 구현

1. **TodoWrite 등록**:
   - Phase 파일의 **Tasks** 섹션을 TodoWrite에 등록
   - Task별 예상 시간도 함께 표시

2. **순서대로 구현**:
   - 각 Task를 하나씩 완료
   - 완료된 Task는 **즉시** TodoWrite에서 completed로 변경
   - Phase 파일에서도 `- [x]`로 업데이트

3. **Non-Goals 준수**:
   - 구현 중 Non-Goals에 해당하는 작업은 절대 하지 않음
   - 범위를 벗어나는 리팩토링/개선 금지

4. **Implementation Notes 기록** (선택):
   - Phase 파일의 "Implementation Notes" 섹션에 발견사항 기록

---

### 5. Phase 검증 (MANDATORY)

**모든 Task 완료 후 반드시 실행**:

1. **빌드 확인**:
   ```bash
   ./gradlew :app:compileDebugKotlin
   ```

2. **verification.md 실행** (있는 경우):
   - Test Commands 실행
   - Manual Checks 수행

3. **Constitution Check** (상세 구조의 경우):
   - `03-technical-plan.md`의 Constitution Check 테이블 검토
   - 원칙 위반 여부 확인
   - 위반이 있다면 Complexity Tracking 테이블에 기록

4. **Phase Verification Prompt** (Phase 파일에 있는 경우):
   - Phase 파일의 "Phase Verification" 섹션 프롬프트 실행

---

### 6. 문서 업데이트 (MANDATORY)

**다음 순서대로 모두 업데이트**:

1. **phases/phase-N.md** (또는 plan.md):
   - Status: `✅ Done`
   - Actual 시간 기록
   - 모든 Tasks `[x]` 체크

2. **README.md** (또는 plan.md의 Progress Status):
   - Progress Status 테이블 업데이트:
     ```markdown
     | Phase N: [Name] | ✅ Done | YYYY-MM-DD | 완료 노트 |
     ```

3. **progress-log.md** (또는 plan.md의 Progress Log):
   - 오늘 날짜로 새 섹션 추가:
     ```markdown
     ## [YYYY-MM-DD]

     ### Done
     - Phase N 완료: [간단 설명]
     - 주요 변경사항: ...

     ### Findings
     - [구현 중 발견한 것]

     ### Next
     - Phase N+1: [다음 할 것]
     ```

---

## 오류 처리

**문서/파일이 없는 경우**:
1. 사용자에게 명확히 알리기: "X 파일이 없습니다"
2. 대안 제시:
   - `.docs/` 디렉토리 구조 보여주기
   - 사용 가능한 Phase 목록 표시
3. 추측하지 말고 사용자 확인 요청

**Phase가 이미 완료된 경우**:
1. 사용자에게 알리기
2. 다시 실행할지, 다음 Phase로 넘어갈지 질문

---

## 성공 기준

다음이 모두 충족되어야 Phase 구현 완료:

- [ ] Context 파일을 모두 읽고 관련 파일들도 실제로 읽었다
- [ ] Non-Goals를 확인하고 준수했다
- [ ] 모든 Tasks가 완료되었다
- [ ] 빌드가 성공했다
- [ ] Constitution Check를 통과했다 (해당하는 경우)
- [ ] README Progress Status가 업데이트되었다
- [ ] progress-log가 업데이트되었다