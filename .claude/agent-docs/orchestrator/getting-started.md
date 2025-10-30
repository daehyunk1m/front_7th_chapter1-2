# 🚀 Orchestrator 에이전트 시작 가이드

> **목적**: Orchestrator 에이전트를 처음 사용하는 사용자를 위한 빠른 시작 가이드
> **대상**: 개발자, 프로젝트 관리자
> **소요 시간**: 약 10분

---

## 📖 문서 읽기 순서

Orchestrator 에이전트를 이해하기 위해 다음 순서로 문서를 읽어주세요:

### 1️⃣ 첫 번째: 역할과 정체성 이해
📄 **[orchestrator.md](../../agents/orchestrator.md)** - "나는 누구인가?"

**읽는 목적**:
- Orchestrator가 무엇을 하는 에이전트인지 이해
- 6단계 TDD 파이프라인 개념 파악
- 언제 이 에이전트를 사용해야 하는지 판단

**핵심 개념**:
- 단일 책임 에이전트 (각 에이전트는 하나의 명확한 역할)
- 문서 기반 인터페이스 (Handoff → Artifact)
- 순수함수 패러다임 (명확한 입력/출력, 재현 가능)

**예상 소요 시간**: 5분

---

### 2️⃣ 두 번째: 입출력 계약 이해
📄 **[contract.md](./contract.md)** - "무엇을 주고받는가?"

**읽는 목적**:
- 각 Phase별 입력/출력 형식 이해
- Handoff 문서 구조 파악
- 검증 기준 확인

**핵심 개념**:
- Input Contract: 사용자 요구사항 → Work Plan
- Output Contract: Work Plan, Handoff Documents, Final Report
- Validation Contract: 각 Phase별 검증 기준
- Isolation Contract: 에이전트 독립성 보장

**예상 소요 시간**: 10분

---

### 3️⃣ 세 번째: 실행 방법 학습
📄 **[prompt.md](./prompt.md)** - "어떻게 하는가?"

**읽는 목적**:
- Phase별 실행 절차 학습
- 산출물 저장 경로 확인
- 검증 프로세스 이해

**핵심 개념**:
- Phase 0-6 실행 가이드
- Handoff 문서 생성 방법
- 품질 게이트 체크리스트

**예상 소요 시간**: 15분

---

## 🎯 빠른 실행 가이드

### 전제 조건

프로젝트 루트에서 다음 파일들이 존재해야 합니다:

- ✅ `CLAUDE.md` - 프로젝트 규칙 및 컨벤션
- ✅ `.claude/docs/folder-tree.md` - 폴더 구조 가이드 (선택)
- ✅ `.claude/agents/orchestrator.md` - Orchestrator 역할 정의
- ✅ `.claude/agent-docs/orchestrator/` - 작업 산출물 저장 폴더

### 폴더 구조 확인

```bash
.claude/
├── agents/
│   └── orchestrator.md           # 역할 정의
└── agent-docs/
    └── orchestrator/
        ├── contract.md            # 계약 명세
        ├── prompt.md              # 실행 매뉴얼
        ├── getting-started.md     # 이 문서
        ├── handoff/               # Phase 전환 문서 (작업 중 생성됨)
        ├── logs/                  # 작업 산출물 (작업 중 생성됨)
        ├── references/            # 참조 자료 (필요 시 생성)
        └── state/                 # 진행 상황 추적 (작업 중 생성됨)
```

---

## 🚀 Orchestrator 사용 방법

### 방법 1: `@` 멘션 사용 (가장 간단 ⭐⭐⭐⭐⭐)

Claude Code의 `@` 멘션 기능을 사용하여 Orchestrator를 즉시 활성화할 수 있습니다.

#### 1. Claude Code 세션 시작

```bash
claude
```

#### 2. `@agent-orchestrator` 멘션 + 요구사항 입력

```text
@agent-orchestrator

이 프로젝트에 반복 일정 기능을 추가하고 싶습니다.
- 반복 유형: 매일, 매주, 매월, 매년
- 31일에 매월 선택 시 매월 31일에만 생성
- 윤년 29일에 매년 선택 시 2월 29일에만 생성
```

**이렇게만 입력하면:**

- ✅ Orchestrator 에이전트가 자동으로 활성화됨
- ✅ `.claude/agents/orchestrator.md` 자동 로드
- ✅ 관련 참조 문서 자동 참조
- ✅ 6단계 TDD 파이프라인 자동 실행

#### 3. Orchestrator가 자동으로 작업 진행

```text
Orchestrator:
"요구사항을 분석하겠습니다.

Phase 0: Planning을 시작합니다.
Work Plan을 작성하겠습니다.

[Work Plan 작성...]

Phase 1: Feature Design을 시작합니다.
feature-designer 에이전트를 호출하겠습니다.

<uses Task tool to launch feature-designer agent>
```

---

### 방법 2: 명시적 역할 부여 (더 세밀한 제어)

더 명확한 제어가 필요한 경우 역할을 명시적으로 부여할 수 있습니다.

#### Step 1. Claude Code 세션 시작

```bash
claude
```

#### Step 2. Orchestrator 역할 명시적 부여 및 요구사항 전달

```text
당신은 Orchestrator 에이전트입니다.

참조 문서:
- .claude/agents/orchestrator.md
- .claude/agent-docs/orchestrator/prompt.md
- CLAUDE.md

이 프로젝트에 반복 일정 기능을 추가하고 싶습니다.
- 반복 유형: 매일, 매주, 매월, 매년
- 31일에 매월 선택 시 매월 31일에만 생성
- 윤년 29일에 매년 선택 시 2월 29일에만 생성
- 반복일정은 일정 겹침 감지 제외

6단계 TDD 파이프라인으로 진행해주세요.
```

**장점:**

- ✅ 워크플로우 명시 ("6단계 TDD 파이프라인")
- ✅ 참조 문서 명확히 지정
- ✅ 가장 예측 가능한 동작

---

### 📊 방법 비교

| 방법 | 입력 길이 | 편의성 | 명확성 | 권장도 |
|------|-----------|--------|--------|--------|
| `@agent-orchestrator` | 짧음 | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ | ⭐⭐⭐⭐⭐ |
| 명시적 역할 부여 | 김 | ⭐⭐ | ⭐⭐⭐⭐⭐ | ⭐⭐⭐⭐ |

**권장:** 대부분의 경우 `@agent-orchestrator` 사용을 권장합니다.

---

## 📝 첫 번째 작업 실행하기

### Step 1: 요구사항 정의

사용자 요구사항을 명확히 정의합니다.

**예시**:

```text
"캘린더에 카테고리별 필터링 기능을 추가하고 싶어요.
사용자가 업무/개인/기타 카테고리를 선택하면 해당 이벤트만 표시됩니다."
```

**좋은 요구사항의 특징**:

- ✅ 구체적인 기능 설명
- ✅ 사용자 관점의 설명
- ✅ 기대하는 동작 명시
- ❌ 구현 세부사항 (Orchestrator가 결정)

---

### Step 2: Phase 0 - 작업 계획 수립

Orchestrator가 요구사항을 분석하고 Work Plan을 작성합니다.

**산출물**:
```
.claude/agent-docs/orchestrator/logs/YYYY-MM-DD_category-filter-plan.md
```

**Work Plan 필수 섹션**:
- 📋 개요 (목표, 범위, 예상 소요 시간)
- 🔨 작업 세분화 (우선순위, 의존성, 담당 에이전트)
- 📊 실행 순서 (Phase 1-6)
- ✅ 품질 검증 포인트
- ⚠️ 리스크 평가

**체크리스트**:
- [ ] Work Plan이 생성되었는가?
- [ ] 모든 필수 섹션이 포함되었는가?
- [ ] Phase 1-6이 명확히 정의되었는가?

---

### Step 3: Phase 1-5 실행

각 Phase마다 다음 프로세스를 반복합니다:

```
1. Orchestrator가 Handoff 문서 생성
   └─> .claude/agent-docs/orchestrator/handoff/phaseN.md

2. 해당 에이전트가 독립 세션으로 실행
   └─> Handoff 문서만 읽고 작업 수행

3. 에이전트가 산출물(Artifact) 생성
   └─> .claude/agent-docs/{agent-name}/logs/YYYY-MM-DD_*.md

4. Orchestrator가 산출물 검증
   └─> 체크리스트 확인, 명령어 실행

5. 검증 통과 → 다음 Phase Handoff 생성
   검증 실패 → 재작업 또는 롤백
```

**Phase별 핵심 검증**:
- **Phase 1 (Feature Design)**: 타입 정의, 컴포넌트 구조, API 설계 완료
- **Phase 2 (Test Design)**: 테스트 케이스, 목킹 전략, GWT 패턴 적용
- **Phase 3 (RED)**: 테스트 실행 결과 = **FAIL** ⭐
- **Phase 4 (GREEN)**: 테스트 실행 결과 = **PASS** ⭐
- **Phase 5 (REFACTOR)**: 코드 품질 개선 + 테스트 여전히 PASS

---

### Step 4: Phase 6 - 최종 검증

Orchestrator가 전체 통합 검증을 수행합니다.

**검증 명령어**:
```bash
pnpm test              # 모든 테스트 실행
pnpm test:coverage     # 커버리지 확인
pnpm lint              # ESLint 검사
pnpm lint:tsc          # TypeScript 컴파일 검사
pnpm dev               # 수동 통합 테스트
```

**최종 산출물**:
```
.claude/agent-docs/orchestrator/logs/YYYY-MM-DD_category-filter-final-report.md
```

**Final Report 필수 내용**:
- 작업 요약 (시작일, 완료일, 소요 시간)
- 완료된 작업 목록
- 품질 검증 결과
- 변경 사항 (신규/수정/삭제 파일)
- 테스트 커버리지
- 남은 기술 부채
- 후속 작업 제안

---

## 🔍 실제 예시: 카테고리 필터 기능 추가

### 요구사항
```
캘린더에 카테고리별 필터링 기능 추가
- 사용자가 "업무", "개인", "기타" 카테고리를 선택
- 선택한 카테고리의 이벤트만 표시
- 여러 카테고리 동시 선택 가능
```

### Phase 0: Planning 산출물

**파일**: `2025-10-30_category-filter-plan.md`

```markdown
## 작업 세분화

1. **타입 정의 확장** (P0)
   - EventForm에 category 필드 추가
   - CategoryType enum 정의
   - 담당: feature-designer
   - 결과물: 업데이트된 src/types.ts

2. **필터링 훅 구현** (P1)
   - useSearch 훅에 카테고리 필터 추가
   - 담당: code-writer
   - 결과물: src/hooks/useSearch.ts

3. **UI 컴포넌트 추가** (P1)
   - 카테고리 선택 체크박스 그룹
   - 담당: code-writer
   - 결과물: src/App.tsx (컴포넌트 분리 고려)

...
```

### Phase 1: Feature Design Handoff

**파일**: `handoff/phase1.md`

```yaml
---
phase: 1
agent: feature-designer
timestamp: 2025-10-30T10:00:00Z
status: ready

inputs:
  requirement: "카테고리별 필터링 기능 추가"
  context_files:
    - CLAUDE.md
    - .claude/agent-docs/orchestrator/logs/2025-10-30_category-filter-plan.md

references:
  agent_definition: ../../agents/feature-designer.md
  agent_prompt: ../feature-designer/prompt.md

output_requirements:
  path: .claude/agent-docs/feature-designer/logs/2025-10-30_category-filter-spec.md
  required_sections:
    - 요구사항 요약
    - 기술 설계
    - 타입 정의
    - 파일 구조
  format: markdown

constraints:
  - CLAUDE.md 컨벤션 준수
  - 기존 useSearch 훅 활용
  - Material-UI 컴포넌트 사용

validation_criteria:
  - CategoryType 타입 정의 완료
  - EventForm 타입 확장 명세
  - 필터링 로직 설계 완료
---

# Phase 1: Feature Design

## 작업 내용
카테고리 필터링 기능에 대한 상세 기술 명세서를 작성하세요.

...
```

### Phase 3: RED 검증

```bash
$ pnpm test task.category-filter.spec.ts

FAIL src/__tests__/task.category-filter.spec.ts
  ✗ 카테고리 필터링이 올바르게 작동한다
  ✗ 여러 카테고리 동시 선택 시 올바르게 필터링한다
  ✗ 모든 카테고리 선택 해제 시 모든 이벤트를 표시한다

Expected: FAIL ✅ (RED Phase 성공)
```

### Phase 4: GREEN 검증

```bash
$ pnpm test task.category-filter.spec.ts

PASS src/__tests__/task.category-filter.spec.ts
  ✓ 카테고리 필터링이 올바르게 작동한다
  ✓ 여러 카테고리 동시 선택 시 올바르게 필터링한다
  ✓ 모든 카테고리 선택 해제 시 모든 이벤트를 표시한다

Expected: PASS ✅ (GREEN Phase 성공)
```

---

## ⚠️ 주의사항

### 1. Phase 순서 엄수

Phase를 건너뛰거나 순서를 바꾸지 마세요:

```
❌ 잘못된 예: Phase 1 → Phase 4 (테스트 없이 구현)
✅ 올바른 예: Phase 1 → Phase 2 → Phase 3 → Phase 4
```

### 2. RED → GREEN 확인

Phase 3에서 테스트가 실패하지 않으면 문제입니다:

```
Phase 3 결과가 PASS인 경우:
1. 테스트가 구현을 검증하지 못하고 있음
2. 이미 구현 코드가 존재함
3. Phase 3 재작업 필요
```

### 3. Handoff 문서의 중요성

Handoff 문서 없이 에이전트를 실행하지 마세요:

```
❌ 직접 에이전트 호출
✅ Handoff 문서 생성 → 에이전트 실행
```

### 4. 독립성 유지

각 에이전트는 Handoff에 명시된 입력만 사용해야 합니다:

```
❌ 이전 Phase의 암묵적 컨텍스트 참조
❌ 다른 에이전트의 내부 상태 접근
✅ Handoff 문서에 명시된 파일만 읽기
```

---

## 🆘 문제 해결

### Q: Phase 검증이 실패했어요

**A**: 다음 절차를 따르세요:

1. 실패 원인 분석 (에러 메시지, 로그 확인)
2. `references/issues-log.md`에 문제 기록
3. 재시도 전략 결정:
   - 같은 Phase 재실행 (Handoff 수정)
   - 이전 Phase로 롤백 (설계 재검토)
4. `state/current-state.json` 업데이트
5. 사용자 승인 후 재시작

**재시도 제한**: 최대 3회까지, 이후 롤백 고려

---

### Q: 산출물이 지정된 경로에 없어요

**A**: 경로를 확인하세요:

```bash
# 산출물 경로 확인
ls -la .claude/agent-docs/{agent-name}/logs/

# Handoff에 명시된 경로와 일치하는지 확인
cat .claude/agent-docs/orchestrator/handoff/phaseN.md
```

---

### Q: 작업 중단 후 어떻게 재개하나요?

**A**: 상태 파일을 확인하세요:

```bash
# 진행 상황 확인
cat .claude/agent-docs/orchestrator/state/current-state.json

# 마지막 완료 Phase 확인
# 다음 Phase Handoff 존재 여부 확인
# 사용자 승인 후 다음 Phase 실행
```

---

### Q: 에이전트가 역할을 벗어난 작업을 했어요

**A**: Handoff 문서를 재검토하세요:

1. `constraints` 섹션이 명확한가?
2. `output_requirements`가 구체적인가?
3. 필요 시 Handoff 수정 후 재실행

---

## 📚 추가 학습 자료

### 프로젝트 규칙
- [CLAUDE.md](../../../CLAUDE.md) - 프로젝트 전체 규칙 및 컨벤션

### 공통 문서
- [folder-tree.md](../../docs/folder-tree.md) - 폴더 구조 가이드
- [rule-of-make-good-test.md](../../docs/rule-of-make-good-test.md) - 테스트 작성 철학

### 다른 에이전트
- [feature-designer.md](../../agents/feature-designer.md) - 기능 설계 전문가
- [test-designer.md](../../agents/test-designer.md) - 테스트 전략 수립
- [test-writer.md](../../agents/test-writer.md) - 테스트 코드 작성
- [code-writer.md](../../agents/code-writer.md) - 구현 코드 작성
- [refactoring-expert.md](../../agents/refactoring-expert.md) - 코드 품질 개선

---

## 🎓 핵심 개념 복습

### 1. 순수함수 패러다임
```typescript
type Agent = (handoff: HandoffDoc) => Artifact;

// 특징:
// - 명확한 입력/출력
// - 부수 효과 없음
// - 같은 입력 → 같은 출력
```

### 2. 문서 기반 인터페이스
```
Orchestrator → Handoff 문서 → Agent → Artifact → Orchestrator
              (명시적 계약)        (검증)
```

### 3. TDD 엄격 적용
```
RED (실패) → GREEN (통과) → REFACTOR (개선)
    ↑           ↑              ↑
  Phase 3    Phase 4        Phase 5
```

---

## ✅ 시작 전 체크리스트

다음 항목을 모두 확인했다면 준비가 완료되었습니다:

- [ ] [orchestrator.md](../../agents/orchestrator.md) 읽기 완료
- [ ] [contract.md](./contract.md) 읽기 완료
- [ ] [prompt.md](./prompt.md) 읽기 완료
- [ ] 프로젝트에 `CLAUDE.md`가 존재함
- [ ] `.claude/agent-docs/orchestrator/` 폴더 구조 확인
- [ ] 6단계 TDD 파이프라인 개념 이해
- [ ] Handoff 프로토콜 이해
- [ ] Phase별 검증 기준 숙지

---

## 🌿 기능별 브랜치 워크플로우

Orchestrator는 각 기능을 독립적인 브랜치에서 개발하여 Git으로 버전을 관리합니다.

### 기본 워크플로우

**Phase 0 시작 시 - Feature 브랜치 생성**:

```bash
# 요구사항: "반복 일정 기능 추가"
# → Feature slug: "repeat-event"
# → 브랜치: feat/repeat-event

git checkout -b feat/repeat-event
```

**각 Phase 완료 후 - 자동 커밋 및 태그**:

```bash
# Phase N 검증 통과 후
git add [산출물]
git commit -m "Phase-N: [한글 기능 설명]

- [한글 상세 내용]
- [변경사항]"

git tag phase-N-[feature-slug]
```

**Phase 6 완료 후 - Main 브랜치 머지**:

```bash
# 1. Feature 완성 태그
git tag feature/repeat-event-v1.0.0

# 2. Main 브랜치 머지
git checkout main
git merge --no-ff feat/repeat-event \
  -m "Feat: 반복 일정 기능 추가

- 매일/매주/매월/매년 반복 유형 지원
- RepeatInfo 타입 활성화 및 UI 통합
- 완료된 Phase: 0~6"
```

### 완전한 워크플로우 예시

```text
사용자: @agent-orchestrator
        반복 일정 기능을 추가하고 싶어요.
        - 매일, 매주, 매월, 매년 반복 유형 선택
        - 반복 종료 날짜 설정

─────────────────────────────────────────────────────
Phase 0: Planning
─────────────────────────────────────────────────────
$ git checkout -b feat/repeat-event

[Work Plan 작성]

$ git add .claude/agent-docs/orchestrator/
$ git commit -m "Phase-0: 반복 일정 기능 계획 수립

- 6단계 TDD 파이프라인 수립
- 타입, 유틸, 훅, UI 작업 세분화"

$ git tag phase-0-repeat-event

─────────────────────────────────────────────────────
Phase 1: DESIGN (feature-designer)
─────────────────────────────────────────────────────
<Task tool: feature-designer 호출>

[spec.md 생성]

$ git add .claude/agent-docs/feature-designer/
$ git commit -m "Phase-1: 반복 일정 기능 설계 완료

- RepeatInfo 타입 정의
- 반복 로직 설계"

$ git tag phase-1-repeat-event

─────────────────────────────────────────────────────
Phase 2-5: [동일 패턴으로 진행]
─────────────────────────────────────────────────────

─────────────────────────────────────────────────────
Phase 6: VALIDATE
─────────────────────────────────────────────────────
$ pnpm test              # ✓ 전체 테스트 통과
$ pnpm lint              # ✓ 품질 검사 통과

$ git add .claude/agent-docs/orchestrator/logs/
$ git commit -m "Phase-6: 반복 일정 기능 최종 검증 완료

- 전체 테스트 통과율: 100%
- TypeScript/ESLint 검사 통과"

$ git tag feature/repeat-event-v1.0.0

─────────────────────────────────────────────────────
Main 브랜치 머지
─────────────────────────────────────────────────────
✅ 모든 Phase 완료!

다음 중 선택하세요:
1. [추천] main 브랜치에 머지
2. PR 생성 (팀 리뷰 필요 시)
3. 추가 작업 계속

[사용자가 1번 선택]

$ git checkout main
$ git merge --no-ff feat/repeat-event \
  -m "Feat: 반복 일정 기능 추가

- 매일/매주/매월/매년 반복 유형 지원
- RepeatInfo 타입 활성화 및 UI 통합
- 완료된 Phase: 0~6"
```

### 검증 실패 시 롤백

Phase 검증 실패 시 Git으로 자동 롤백합니다:

```bash
# Phase 3 검증 실패 시나리오
Phase 3: RED 검증 결과 → ✗ 테스트가 통과함 (잘못됨)

# 자동 롤백 (Phase 2로 되돌림)
$ git reset --hard phase-2-repeat-event
$ git tag -d phase-3-repeat-event

# 원인 분석 및 재작업
실패 원인: 테스트 파일에 구현 코드가 포함됨
해결 방안: handoff/phase3-v2.md 생성 (제약조건 강화)

# Phase 3 재실행
<Task tool: test-writer 재호출>
```

### 병렬 기능 개발

여러 기능을 동시에 작업할 수 있습니다:

```bash
# 브랜치 구조
main
  ├── feat/repeat-event (Phase 4 진행 중)
  ├── feat/category-filter (Phase 0 완료)
  └── feat/drag-drop (대기 중)

# 각 브랜치는 독립적으로 Phase 진행
# Phase 태그도 feature-slug로 구분됨:
# - phase-0-repeat-event
# - phase-0-category-filter

# 완료 순서대로 main에 머지
```

### 커밋 메시지 규칙

**기본 원칙**:

- **말머리**: 파스칼케이스 영어 (Phase-N:, Feat:, Fix:)
- **타이틀/본문**: 한글
- **Claude 서명**: 제외

**Phase 커밋 예시**:

```text
Phase-2: 반복 일정 테스트 전략 수립

- 테스트 케이스 설계 완료
- 목킹 전략 및 GWT 패턴 적용
```

**Feature 완료 커밋 예시**:

```text
Feat: 반복 일정 기능 추가

- 매일/매주/매월/매년 반복 유형 지원
- RepeatInfo 타입 활성화 및 UI 통합
- 완료된 Phase: 0~6
```

### 브랜치 네이밍 규칙

- `feat/[feature-slug]` - 새 기능 (예: feat/repeat-event)
- `fix/[bug-slug]` - 버그 수정 (예: fix/overlap-detection)
- `refactor/[scope-slug]` - 리팩토링 (예: refactor/event-utils)

**Feature slug 생성 방법**:

- 요구사항의 핵심 키워드 추출
- kebab-case로 작성
- 2-3 단어 추천

예시:

- "반복 일정 기능 추가" → `repeat-event`
- "카테고리 필터링 추가" → `category-filter`
- "드래그 앤 드롭으로 일정 변경" → `drag-drop`

### Git 히스토리 예시

```bash
$ git log --oneline --graph

* 8a9b2c3 (tag: feature/repeat-event-v1.0.0) Phase-6: 반복 일정 기능 최종 검증 완료
* 7f8e1d2 (tag: phase-5-repeat-event) Phase-5: 반복 일정 기능 REFACTOR 단계 완료
* 6d7c0a1 (tag: phase-4-repeat-event) Phase-4: 반복 일정 기능 GREEN 단계 완료
* 5b6a9f0 (tag: phase-3-repeat-event) Phase-3: 반복 일정 기능 RED 단계 완료
* 4a5b8e9 (tag: phase-2-repeat-event) Phase-2: 반복 일정 테스트 전략 수립
* 3f4e7d8 (tag: phase-1-repeat-event) Phase-1: 반복 일정 기능 설계 완료
* 2d3c6a7 (tag: phase-0-repeat-event) Phase-0: 반복 일정 기능 계획 수립
* 1a2b5c6 (HEAD -> main) Docs: 프로젝트 규칙 업데이트
```

---

**다음 단계**: 실제 작업을 시작하려면 사용자 요구사항을 Orchestrator에게 전달하세요.

**문의**: 문제가 발생하면 `references/issues-log.md`에 기록하고 문서를 재확인하세요.

---

**마지막 업데이트**: 2025-10-30
**버전**: 1.0.0
**작성자**: Orchestrator 개발팀
