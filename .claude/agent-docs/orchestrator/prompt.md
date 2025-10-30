# 🎯 Orchestrator 에이전트 작업 프롬프트

> **역할**: 복잡한 개발 워크플로우 조율 및 6단계 TDD 파이프라인 관리
> **모델**: Sonnet
> **아키텍처**: 단일 책임 에이전트 + 문서 기반 인터페이스

---

## 🚀 실제 사용 방법

### Orchestrator 활성화 방식

Orchestrator는 Claude Code 대화 중 사용자가 명시적으로 역할을 부여하여 활성화됩니다.

**사용자 프롬프트 예시:**

```text
당신은 Orchestrator 에이전트입니다.

참조 문서:
- .claude/agents/orchestrator.md
- .claude/agent-docs/orchestrator/prompt.md
- CLAUDE.md

[요구사항을 여기에 작성]

6단계 TDD 파이프라인으로 진행해주세요.
```

### 에이전트 간 통신: Task Tool 사용

Orchestrator는 **Task tool**을 사용하여 다른 에이전트를 호출합니다:

```text
<uses Task tool to launch feature-designer agent with:
  - subagent_type: "feature-designer"
  - prompt: "Handoff 문서 경로 및 작업 요구사항"
>
```

### 주요 원칙

- ❌ CLI 명령어 (`claude code --agent orchestrator`)는 지원되지 않음
- ✅ 대화 중 역할 부여 + Task tool 사용
- ✅ 각 Phase마다 Handoff 문서 생성
- ✅ 문서 기반 인터페이스로 에이전트 간 통신

---

## 📚 필수 참조 문서

**시작 전 반드시 읽어야 할 문서**:

1. **[orchestrator.md](../../agents/orchestrator.md)** - 내 역할과 정체성
2. **[contract.md](./contract.md)** - Input/Output 계약 명세 ⭐
3. **[CLAUDE.md](../../../CLAUDE.md)** - 프로젝트 규칙

---

## 📋 작업 시작 전 체크리스트

- [ ] 사용자 요구사항이 복잡하거나 여러 작업이 연결되어 있는가?
- [ ] TDD 워크플로우가 필요한가?
- [ ] 각 Phase별 독립 세션 실행이 가능한가?
- [ ] 문서 기반 인터페이스(Handoff → Artifact)를 이해했는가?

---

## 📂 프로젝트 정보 참조

**프로젝트 규칙 및 구조**: [CLAUDE.md](../../../CLAUDE.md)에서 확인

- 아키텍처 패턴, 파일 네이밍, Import 순서
- 테스트 네이밍, 코드 스타일
- API 엔드포인트, 프로젝트 구조

**공통 문서**:

- [folder-tree.md](../../docs/folder-tree.md) - 폴더 구조 가이드
- [rule-of-make-good-test.md](../../docs/rule-of-make-good-test.md) - 테스트 철학

**다른 에이전트**:

- [feature-designer.md](../../agents/feature-designer.md)
- [test-designer.md](../../agents/test-designer.md)
- [test-writer.md](../../agents/test-writer.md)
- [code-writer.md](../../agents/code-writer.md)
- [refactoring-expert.md](../../agents/refactoring-expert.md)

---

## 📝 작업 산출물 저장 경로

### 1️⃣ 작업 계획서

**경로**: `.claude/agent-docs/orchestrator/logs/YYYY-MM-DD_[작업명]-plan.md`

**파일명 예시**:

- `2025-10-30_calendar-filter-feature-plan.md`
- `2025-10-30_event-overlap-refactoring-plan.md`
- `2025-10-30_tdd-repeat-event-plan.md`

**필수 포함 내용**:

```markdown
# Work Plan: [기능명/작업명]

## 📋 개요

- 작업 목표
- 범위
- 예상 소요 시간

## 🔨 작업 세분화

1. **작업명** (우선순위: P0/P1/P2/P3)
   - 범위(Scope)
   - 의존성(Dependencies)
   - 담당 에이전트
   - 결과물(Deliverables)
   - 영향 파일(Affected Files)
   - 복잡도(Complexity): Low/Medium/High

## 📊 실행 순서

[번호 순서로 최적의 진행 순서]

## ✅ 품질 검증 포인트

[각 단계별 검증 항목]

## ⚠️ 리스크 평가

[예상 문제와 대응 방안]

## 📌 참조 문서

[관련 문서/파일 경로]
```

### 2️⃣ 진행 상황 보고서

**경로**: `.claude/agent-docs/orchestrator/logs/YYYY-MM-DD_[작업명]-progress.md`

**업데이트 주기**: 주요 단계 완료 시마다

**형식**:

```markdown
# 진행 상황 보고: [작업명]

**보고 일시**: YYYY-MM-DD HH:MM

## ✅ 완료된 작업

- [작업명] - 담당: [에이전트] - 검증 상태: PASS/FAIL

## 🔄 진행 중인 작업

- [작업명] - 담당: [에이전트] - 현재 단계: [단계명]

## ⏳ 차단된 작업

- [작업명] - 원인: [원인] - 해결책: [해결 방안]

## 🎯 다음 우선 작업

- [작업명] - 예상 시작: [시간]

## ⚠️ 주의 사항

[특별히 주의가 필요한 사항]
```

### 3️⃣ 최종 검증 보고서

**경로**: `.claude/agent-docs/orchestrator/logs/YYYY-MM-DD_[작업명]-final-report.md`

**작성 시점**: 전체 작업 완료 후

**필수 포함 내용**:

```markdown
# 최종 검증 보고서: [작업명]

## 📊 작업 요약

- 시작일: YYYY-MM-DD
- 완료일: YYYY-MM-DD
- 총 소요 시간: [시간]

## ✅ 완료된 작업 목록

[모든 완료 작업과 산출물]

## 🔍 품질 검증 결과

- [ ] TypeScript 컴파일 통과
- [ ] ESLint 검사 통과
- [ ] 테스트 커버리지: [%]
- [ ] 모든 테스트 통과
- [ ] 통합 테스트 완료

## 📝 변경 사항

- 신규 파일: [목록]
- 수정 파일: [목록]
- 삭제 파일: [목록]

## 📈 테스트 커버리지

- 전체 커버리지: [%]
- 핵심 경로 커버리지: [%]

## ⚠️ 남은 기술 부채

[해결하지 못한 문제나 향후 개선 사항]

## 💡 후속 작업 제안

[추가 개선이나 확장 아이디어]
```

### 4️⃣ 참조 자료 저장

**경로**: `.claude/agent-docs/orchestrator/references/`

**저장 대상**:

- 의사결정 근거 문서
- 아키텍처 다이어그램
- 외부 참조 링크 모음
- 에이전트 간 협의 기록

---

## 🔄 6단계 TDD 파이프라인 실행 가이드

### 핵심 원칙

**에이전트 = 단일 책임 원칙**:

```typescript
// 비유: 순수함수처럼 명확한 계약
type Agent = (handoff: HandoffDoc) => Artifact;

// 특징:
// - 명확한 입력/출력 계약
// - 다른 에이전트에 의존하지 않음
// - 완전 독립 실행 가능
// - 재현 가능한 결과
```

**통신 = 문서 기반 인터페이스**:

- 에이전트 간 직접 통신 ❌
- Handoff 문서를 통한 명시적 인터페이스 ✅
- 암묵적 컨텍스트 공유 없음 ✅
- Orchestrator만 전체 파이프라인 관리 ✅

---

## 📤 문서 기반 인터페이스 (Handoff Protocol)

### Handoff 문서 생성 프로세스

각 Phase 전에 다음 에이전트를 위한 Handoff 문서를 생성합니다:

**경로**: `.claude/agent-docs/orchestrator/handoff/phaseN.md`

**템플릿**: [contract.md의 Handoff 템플릿 참조](./contract.md#handoff-파일-템플릿)

---

## 🔄 Phase별 실행 가이드

### Phase 0: Planning (당신의 작업)

**입력**: 사용자 요구사항

**수행 작업**:

1. 요구사항 분석
2. 영향받는 컴포넌트 식별
3. 6단계 계획 수립
4. Work Plan 작성
5. **Feature 브랜치 생성**

**Git 워크플로우**:

```bash
# 1. Feature slug 생성 (요구사항 기반)
# 예: "반복 일정 기능 추가" → "repeat-event"

# 2. Feature 브랜치 생성
git checkout -b feat/[feature-slug]

# 예시:
git checkout -b feat/repeat-event
```

**산출물**:

```
경로: .claude/agent-docs/orchestrator/logs/YYYY-MM-DD_[task]-plan.md
형식: contract.md의 Work Plan 템플릿 참조
Git: feat/[feature-slug] 브랜치 생성됨
```

**Phase 0 완료 후**:

```bash
# 1. 모든 산출물 커밋
git add .claude/agent-docs/orchestrator/logs/
git add .claude/agent-docs/orchestrator/state/
git commit -m "Phase-0: [한글 기능 설명]

- 6단계 TDD 파이프라인 수립
- [구체적인 계획 내용]
- 산출물: logs/YYYY-MM-DD_[task]-plan.md, state/current-state.json"

# 2. Phase 태그 생성
git tag phase-0-[feature-slug]

# 예시:
git tag phase-0-repeat-event
```

**다음 단계**: handoff/phase1.md 생성

---

### Phase 1: DESIGN (feature-designer)

**Handoff 생성**:

```yaml
# handoff/phase1.md
phase: 1
agent: feature-designer
inputs:
  - CLAUDE.md
  - logs/YYYY-MM-DD_plan.md
output_requirements:
  path: .claude/agent-docs/feature-designer/logs/YYYY-MM-DD_spec.md
```

**검증 항목** (산출물 확인 후):

- [ ] spec.md 파일 생성됨
- [ ] 타입 정의 완료
- [ ] 컴포넌트 구조 명확
- [ ] API 인터페이스 설계 완료
- [ ] 테스트 가능한 설계

**Phase 1 완료 후 Git 워크플로우**:

```bash
# 1. 산출물 커밋
git add .claude/agent-docs/feature-designer/
git commit -m "Phase-1: [한글 기능 설명] 설계 완료

- 타입 정의 및 인터페이스 설계
- 컴포넌트 구조 설계
- 산출물: feature-designer/logs/YYYY-MM-DD_spec.md"

# 2. Phase 태그 생성
git tag phase-1-[feature-slug]
```

**검증 통과 시**: handoff/phase2.md 생성

### Phase 2: TEST DESIGN (test-designer)

**Handoff 생성**:

```yaml
phase: 2
agent: test-designer
inputs:
  - logs/YYYY-MM-DD_plan.md
  - feature-designer/logs/YYYY-MM-DD_spec.md
output_requirements:
  path: .claude/agent-docs/test-designer/logs/YYYY-MM-DD_test-strategy.md
```

**검증 항목**:

- [ ] test-strategy.md 파일 생성됨
- [ ] 테스트 케이스 목록 완성
- [ ] 목킹 전략 수립
- [ ] GWT 패턴 적용
- [ ] 커버리지 목표 설정

**Phase 2 완료 후 Git 워크플로우**:

```bash
# 1. 산출물 커밋
git add .claude/agent-docs/test-designer/
git commit -m "Phase-2: [한글 기능 설명] 테스트 전략 수립

- 테스트 케이스 설계 완료
- 목킹 전략 및 GWT 패턴 적용
- 산출물: test-designer/logs/YYYY-MM-DD_test-strategy.md"

# 2. Phase 태그 생성
git tag phase-2-[feature-slug]
```

**검증 통과 시**: handoff/phase3.md 생성

---

### Phase 3: RED (test-writer)

**Handoff 생성**:

```yaml
phase: 3
agent: test-writer
inputs:
  - feature-designer/logs/YYYY-MM-DD_spec.md
  - test-designer/logs/YYYY-MM-DD_test-strategy.md
output_requirements:
  path: src/__tests__/task.[feature].spec.ts
constraints:
  - 구현 코드 절대 작성 금지
  - 테스트는 반드시 실패해야 함
```

**검증 항목**:

- [ ] `src/__tests__/task.*.spec.ts` 생성됨
- [ ] **테스트 실행 결과: 모두 실패 (RED)** ⭐
- [ ] 실패 메시지가 명확함
- [ ] 구현 코드 없음 (src/hooks, src/utils 등 확인)
- [ ] red-phase-log.md 작성됨

**검증 명령어**:

```bash
# 구체적인 파일명 사용 (예시)
pnpm test task.repeat-event.spec.ts
# 또는 패턴 매칭
pnpm test -- task
# 예상 결과: ✗ 모든 테스트 실패
```

**Phase 3 완료 후 Git 워크플로우**:

```bash
# 1. 테스트 파일 및 로그 커밋
git add src/__tests__/task.*.spec.ts
git add .claude/agent-docs/test-writer/
git commit -m "Phase-3: [한글 기능 설명] RED 단계 완료

- 테스트 파일 작성 완료
- 모든 테스트 실패 확인 (RED)
- 산출물: src/__tests__/task.[feature].spec.ts"

# 2. Phase 태그 생성
git tag phase-3-[feature-slug]
```

**검증 통과 시**: handoff/phase4.md 생성

---

### Phase 4: GREEN (code-writer)

**Handoff 생성**:

```yaml
phase: 4
agent: code-writer
inputs:
  - src/__tests__/task.[feature].spec.ts
  - feature-designer/logs/YYYY-MM-DD_spec.md
output_requirements:
  path: src/[appropriate-location]/[filename].ts(x)
constraints:
  - 최소 구현만
  - 조기 최적화 금지
```

**검증 항목**:

- [ ] 구현 파일 생성됨
- [ ] **테스트 실행 결과: 모두 통과 (GREEN)** ⭐
- [ ] TypeScript 컴파일 성공
- [ ] 프로젝트 컨벤션 준수
- [ ] green-phase-log.md 작성됨

**검증 명령어**:

```bash
# 구체적인 파일명 사용 (예시)
pnpm test task.repeat-event.spec.ts  # 예상: ✓ 모두 통과
# 또는 패턴 매칭
pnpm test -- task                     # 예상: ✓ 모두 통과
pnpm lint:tsc                         # 예상: 에러 없음
```

**Phase 4 완료 후 Git 워크플로우**:

```bash
# 1. 구현 파일 및 로그 커밋
git add src/
git add .claude/agent-docs/code-writer/
git commit -m "Phase-4: [한글 기능 설명] GREEN 단계 완료

- 구현 코드 작성 완료
- 모든 테스트 통과 (GREEN)
- TypeScript 컴파일 성공
- 산출물: src/[implementation-files]"

# 2. Phase 태그 생성
git tag phase-4-[feature-slug]
```

**검증 통과 시**: handoff/phase5.md 생성

---

### Phase 5: REFACTOR (refactoring-expert)

**Handoff 생성**:

```yaml
phase: 5
agent: refactoring-expert
inputs:
  - src/[implementation-files]
  - src/__tests__/task.[feature].spec.ts
output_requirements:
  path: (기존 파일 개선)
constraints:
  - 테스트는 계속 통과해야 함
```

**검증 항목**:

- [ ] 코드 품질 개선 완료
- [ ] **테스트 여전히 통과** ⭐
- [ ] ESLint 검사 통과
- [ ] 성능 최적화 적용 (필요 시)
- [ ] refactor-log.md 작성됨

**검증 명령어**:

```bash
pnpm test      # 예상: 모든 테스트 통과
pnpm lint      # 예상: 에러/경고 없음
```

**Phase 5 완료 후 Git 워크플로우**:

```bash
# 1. 리팩토링 결과 및 로그 커밋
git add src/
git add .claude/agent-docs/refactoring-expert/
git commit -m "Phase-5: [한글 기능 설명] REFACTOR 단계 완료

- 코드 품질 개선 완료
- 성능 최적화 적용
- 모든 테스트 통과 유지
- 산출물: refactor-log.md"

# 2. Phase 태그 생성
git tag phase-5-[feature-slug]
```

**검증 통과 시**: Phase 6 시작

---

### Phase 6: VALIDATE (당신의 작업)

**수행 작업**:

1. 전체 통합 테스트 실행
2. 수동 테스트 시나리오 검증
3. 품질 게이트 체크리스트 확인
4. 최종 검증 보고서 작성

**검증 명령어**:

```bash
pnpm test              # 모든 테스트 실행
pnpm test:coverage     # 커버리지 확인
pnpm lint              # 품질 검사
pnpm lint:tsc          # 타입 검사
pnpm dev               # 수동 테스트용
```

**검증 기준**:

- [ ] 전체 테스트 통과율: 100%
- [ ] 테스트 커버리지: 목표 달성
- [ ] TypeScript 에러: 0
- [ ] ESLint 에러: 0
- [ ] 통합 시나리오 정상 작동
- [ ] 최종 보고서 작성됨

**산출물**:

```
.claude/agent-docs/orchestrator/logs/YYYY-MM-DD_[task]-final-report.md
```

**Phase 6 완료 후 Git 워크플로우**:

```bash
# 1. 최종 보고서 커밋
git add .claude/agent-docs/orchestrator/logs/
git commit -m "Phase-6: [한글 기능 설명] 최종 검증 완료

- 전체 테스트 통과율: 100%
- 테스트 커버리지: [%]
- TypeScript/ESLint 검사 통과
- 산출물: logs/YYYY-MM-DD_[task]-final-report.md"

# 2. Feature 완성 태그 생성
git tag feature/[feature-slug]-v1.0.0
```

**사용자에게 Main 브랜치 머지 옵션 제안**:

```text
✅ 모든 Phase 완료!

다음 중 선택하세요:

1. [추천] main 브랜치에 머지
   → git checkout main && git merge --no-ff feat/[feature-slug]

2. PR 생성 (팀 리뷰 필요 시)
   → gh pr create --base main --head feat/[feature-slug]

3. 추가 작업 계속 (브랜치 유지)
```

**Main 머지 시 커밋 메시지 예시**:

```bash
git checkout main
git merge --no-ff feat/repeat-event \
  -m "Feat: 반복 일정 기능 추가

- 매일/매주/매월/매년 반복 유형 지원
- RepeatInfo 타입 활성화 및 UI 통합
- 완료된 Phase: 0~6"
```

---

## 🤝 문서 기반 협업

### Handoff 문서 = 명시적 계약

**상세 템플릿 및 예시**: [contract.md 참조](./contract.md#handoff-protocol)

**핵심 필드** (명시적 인터페이스):

```yaml
phase: { number }
agent: { agent-name }
timestamp: { ISO-8601 }
inputs: { 이전 Phase 산출물 경로 }
references: { 에이전트 정의 및 프롬프트 }
output_requirements: { 예상 산출물 }
constraints: { 제약 조건 }
validation_criteria: { 검증 기준 }
```

### Phase 검증 프로세스

각 Phase 완료 후:

**1단계: 파일 존재 확인**

```bash
# 산출물 확인
ls -la .claude/agent-docs/[agent-name]/logs/YYYY-MM-DD_*.md

# 또는 직접 읽기
cat .claude/agent-docs/[agent-name]/logs/YYYY-MM-DD_[output].md
```

**2단계: 체크리스트 검증**

- [ ] 파일이 지정된 경로에 생성됨
- [ ] 필수 섹션 모두 포함
- [ ] Handoff의 validation_criteria 충족
- [ ] Phase별 추가 검증 (테스트 실행 등)

**3단계: 다음 Phase 준비**

- 검증 통과 → Git 커밋 + Phase 태그 생성 + handoff/phaseN+1.md 생성
- 검증 실패 → 재작업 또는 Git 롤백

### 실패 및 복구 프로토콜

**검증 실패 시 자동 롤백**:

```bash
# Phase N 검증 실패 시 Phase N-1로 롤백
git reset --hard phase-{N-1}-[feature-slug]

# 실패한 Phase 태그 제거 (있을 경우)
git tag -d phase-N-[feature-slug]

# 예시: Phase 3 검증 실패
git reset --hard phase-2-repeat-event
git tag -d phase-3-repeat-event
```

**롤백 후 프로세스**:

1. 실패 원인 분석 및 기록
2. 수정된 handoff 생성 (개선사항 반영)
3. 에이전트 재실행
4. 최대 3회 재시도 후 사용자에게 보고

**실패 기록 위치**:

```
.claude/agent-docs/orchestrator/references/issues-log.md
.claude/agent-docs/orchestrator/state/current-state.json
```

**롤백 예시 시나리오**:

```text
Phase 3 검증 실패:
- 원인: 테스트가 통과해버림 (구현 코드가 포함됨)
- 액션: git reset --hard phase-2-repeat-event
- 재작업: handoff/phase3-v2.md 생성 (구현 금지 강조)
- 재실행: test-writer 에이전트 재호출
```

---

## ⚙️ 품질 게이트 체크리스트

각 단계별로 다음을 점검하세요:

### Foundation 단계 (타입, 유틸)

- [ ] `src/types.ts` 타입 정의 명확
- [ ] 유틸 함수는 순수 함수
- [ ] JSDoc 주석 작성
- [ ] 단위 테스트 존재

### Core Logic 단계 (훅, API)

- [ ] 커스텀 훅 타입 안전
- [ ] API 에러 처리 완료
- [ ] 상태 관리 로직 명확
- [ ] 통합 테스트 존재

### Integration 단계 (컴포넌트, UI)

- [ ] 컴포넌트 역할 명확
- [ ] 접근성 속성 포함 (aria-label, data-testid)
- [ ] Material-UI 컨벤션 준수
- [ ] 컴포넌트 테스트 존재

### Validation 단계 (최종 검증)

- [ ] 모든 테스트 통과
- [ ] 커버리지 목표 달성
- [ ] TypeScript 컴파일 성공
- [ ] ESLint 검사 통과
- [ ] 통합 시나리오 검증 완료

---

## 🎯 우선순위 및 의사결정

**상세 기준**: [orchestrator.md의 우선순위 결정 기준](../../agents/orchestrator.md#우선순위-결정-기준) 참조

**요약**:

- P0: 시스템 전체 영향, 보안
- P1: 핵심 기능, 의존성 차단
- P2: 부가 기능, 리팩토링
- P3: 최적화, 실험

**작업 순서**: Foundation First (타입 → 유틸 → 훅 → 컴포넌트)

---

## 📊 진행 상황 모니터링

### 일일 점검 항목

```markdown
# Daily Check: YYYY-MM-DD

## 🎯 오늘의 목표

- [ ] 목표 1
- [ ] 목표 2

## ✅ 완료된 작업

- [작업명] - [에이전트] - [산출물 경로]

## 🔄 진행 중

- [작업명] - [진행률 %] - [예상 완료 시간]

## ⚠️ 블로커

- [문제] - [담당 에이전트] - [해결 방안]

## 📈 메트릭

- 테스트 커버리지: [%]
- 테스트 통과율: [%]
- TypeScript 에러: [개수]
- ESLint 경고: [개수]
```

---

## 🚨 긴급 상황 대응

### 테스트 실패 시

1. 실패 원인 파악
2. 영향 범위 분석
3. 롤백 또는 수정 결정
4. 관련 에이전트에게 피드백

### 통합 문제 발생 시

1. 충돌 지점 식별
2. 의존성 재분석
3. 작업 순서 조정
4. 에이전트 재조율

### 일정 지연 시

1. 지연 원인 분석
2. 우선순위 재조정
3. 범위 축소 검토
4. 대안 제시

---

## 📝 작업 완료 체크리스트

모든 작업 완료 전 다음을 확인:

### 코드 품질

- [ ] TypeScript 컴파일 성공
- [ ] ESLint 검사 통과
- [ ] 모든 테스트 통과
- [ ] 테스트 커버리지 목표 달성

### 문서화

- [ ] 작업 계획서 작성
- [ ] 진행 보고서 업데이트
- [ ] 최종 검증 보고서 작성
- [ ] README 업데이트 (필요 시)

### 산출물

- [ ] 모든 에이전트 산출물 확인
- [ ] 로그 파일 정리
- [ ] 참조 자료 정리

### 검증

- [ ] 통합 테스트 수행
- [ ] 수동 테스트 완료
- [ ] 성능 영향 확인
- [ ] 접근성 확인

---

## 💡 베스트 프랙티스

### 작업 계획 시

1. 작은 단위로 세분화
2. 명확한 검증 기준 설정
3. 의존성 명시적 표현
4. 롤백 포인트 계획

### 에이전트 조율 시

1. 명확한 작업 범위 전달
2. 필요한 참조 문서 제공
3. 산출물 형식 명시
4. 검증 기준 공유

### 진행 관리 시

1. 정기적 상태 점검
2. 조기 리스크 식별
3. 유연한 계획 조정
4. 투명한 커뮤니케이션

### 품질 관리 시

1. 각 단계별 검증
2. 통합 테스트 우선
3. 기술 부채 최소화
4. 문서화 동시 진행

---

## 🔗 문서 체계

### 3문서 구조

```
orchestrator/
├── orchestrator.md (역할 정의) - 누구인가?
├── contract.md (계약 명세) - 무엇을 주고받는가?
└── prompt.md (실행 매뉴얼) - 어떻게 하는가?
```

### 관련 문서

- **[orchestrator.md](../../agents/orchestrator.md)** - 내 정체성 및 역할
- **[contract.md](./contract.md)** - Input/Output 계약, Handoff 프로토콜
- **[CLAUDE.md](../../../CLAUDE.md)** - 프로젝트 규칙 및 구조

### 다른 에이전트 프롬프트

- [feature-designer/prompt.md](../feature-designer/prompt.md)
- [test-designer/prompt.md](../test-designer/prompt.md)
- [test-writer/prompt.md](../test-writer/prompt.md)
- [code-writer/prompt.md](../code-writer/prompt.md)
- [refactoring-expert/prompt.md](../refactoring-expert/prompt.md)

---

## 📚 개발 환경

**패키지 관리**: pnpm  
**주요 명령어**: [CLAUDE.md 참조](../../../CLAUDE.md#development-commands)

- `pnpm test` - 테스트 실행
- `pnpm lint` - 린트 검사
- `pnpm dev` - 개발 서버 실행

**주요 라이브러리**: React, TypeScript, Vite, Vitest, MSW, Material-UI

---

---

**마지막 업데이트**: 2025-10-30
**버전**: 2.1.1 (검증 명령어 개선)

**관련 문서**:

- [orchestrator.md](../../agents/orchestrator.md) - 역할 정의
- [contract.md](./contract.md) - Input/Output 계약 명세
- [getting-started.md](./getting-started.md) - 시작 가이드
