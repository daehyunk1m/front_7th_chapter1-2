# Refactoring Expert Agent Documentation

> **Phase 5 (REFACTOR)**: TDD의 리팩터링 단계를 담당하는 전문 에이전트

---

## 📚 문서 구조

### 1. [contract.md](./contract.md) - 계약 명세서
**목적**: Phase 5의 입/출력 계약 정의

**주요 내용:**
- 입력 계약 (Handoff 문서 형식)
- 출력 계약 (리팩터링 보고서 구조)
- 검증 기준 (Phase 5 완료 조건)
- 격리 계약 (권한 및 제약)
- 에러 처리 프로토콜

**대상 독자**: Orchestrator, 다른 에이전트

### 2. [prompt.md](./prompt.md) - 실행 매뉴얼
**목적**: Refactoring Expert의 상세 작업 절차

**주요 내용:**
- 역할 정의
- 8단계 작업 프로세스
- 리팩터링 패턴 및 예시
- 검증 방법
- 트러블슈팅

**대상 독자**: Refactoring Expert 에이전트

### 3. [getting-started.md](./getting-started.md) - 시작 가이드
**목적**: 빠른 시작 및 실전 예시

**주요 내용:**
- 5분 빠른 시작
- 체크리스트
- 일반적인 리팩터링 패턴
- 완전한 예시 세션
- FAQ

**대상 독자**: 처음 사용하는 개발자

---

## 🎯 Refactoring Expert란?

### 핵심 역할

TDD의 **REFACTOR 단계**를 담당:
```
🔴 RED → 🟢 GREEN → 🔵 REFACTOR
                       ↑
                    여기서 작업
```

### 주요 책임

1. **DRY 원칙 적용** - 코드 중복 제거
2. **React 성능 최적화** - memo, useMemo, useCallback
3. **타입 안전성 강화** - any 타입 완전 제거
4. **가독성 향상** - 변수명, 상수화, 함수 분리
5. **디자인 패턴 적용** - 관심사 분리, 전략 패턴 등
6. **문서화** - 리팩터링 근거 명확히 기록

### 핵심 제약

- ⚠️ **테스트는 절대 깨지면 안 됨** (필수)
- ⚠️ **기능 변경 금지** (동작 보존)
- ⚠️ **테스트 파일 수정 금지** (프로덕션 코드만)

---

## 🚀 빠른 시작

### Step 1: 문서 읽기

```bash
# Handoff 문서 확인
cat .claude/agent-docs/orchestrator/handoff/phase5.md
```

### Step 2: 현재 상태 파악

```bash
# 구현 코드 및 테스트 읽기
cat src/utils/repeatUtils.ts
cat src/__tests__/task.repeat-event.spec.ts

# 테스트 실행 (통과 확인)
pnpm test task.repeat-event.spec.ts
```

### Step 3: 리팩터링 (작은 단위)

```typescript
// 예: any 타입 제거
// Before
export const processData = (data: any) => { ... }

// After
export const processData = (data: DataItem[]): Result[] => { ... }
```

### Step 4: 즉시 테스트

```bash
pnpm test task.repeat-event.spec.ts
```

### Step 5: 보고서 작성

```bash
# 리팩터링 보고서 생성
cat > .claude/agent-docs/refactoring-expert/logs/refactor-report.md
```

---

## 📋 체크리스트

### 완료 조건

- [ ] 모든 테스트 통과 유지 ⚠️
- [ ] any 타입 완전 제거
- [ ] 코드 중복 제거
- [ ] TypeScript 에러 없음
- [ ] ESLint 경고 없음
- [ ] 빌드 성공
- [ ] 리팩터링 보고서 작성

### 검증 명령어

```bash
pnpm test && \
pnpm lint:tsc && \
pnpm lint:eslint && \
pnpm build && \
echo "✅ Phase 5 완료!"
```

---

## 🎓 리팩터링 우선순위

```
1. 타입 안전성 강화 ✅ (가장 안전)
   ↓
2. 코드 중복 제거 ✅
   ↓
3. 가독성 개선 ✅
   ↓
4. 성능 최적화 ⚠️ (주의 필요)
   ↓
5. 디자인 패턴 적용 ⚠️ (가장 위험)
```

각 단계마다 테스트 실행!

---

## 📊 주요 지표

### 개선 전후 비교

| 지표 | Before | After | 목표 |
|------|--------|-------|------|
| any 타입 | N개 | 0개 | -100% |
| 코드 중복 | N곳 | 0곳 | -100% |
| ESLint 경고 | N개 | 0개 | -100% |
| 테스트 통과 | 100% | 100% | 유지 ✅ |

---

## 🔗 관련 문서

### 필수 읽기

- [contract.md](./contract.md) - 입/출력 계약
- [prompt.md](./prompt.md) - 실행 매뉴얼
- [getting-started.md](./getting-started.md) - 시작 가이드

### 참조

- [../../agents/refactoring-expert.md](../../agents/refactoring-expert.md) - 역할 정의
- [../orchestrator/contract.md](../orchestrator/contract.md) - Orchestrator 계약
- [../code-writer/contract.md](../code-writer/contract.md) - Phase 4 계약
- [../../../CLAUDE.md](../../../CLAUDE.md) - 프로젝트 규칙

---

## ❓ FAQ

### Q: 테스트가 깨지면?
**A:** 즉시 변경 되돌리고, 더 작은 단위로 재시도

### Q: 언제 React 최적화?
**A:** 명확한 성능 이점이 측정될 때만 (성급한 최적화 지양)

### Q: 모든 코드를 리팩터링?
**A:** 아니요. 우선순위에 따라 선택적으로

### Q: 새로운 버그 발견?
**A:** 버그 수정은 별도 작업. 문서에만 기록

### Q: 테스트 커버리지 부족?
**A:** 제안만 문서화. 테스트 추가는 Test Writer 역할

---

## 📞 도움말

### 이슈 기록

```markdown
# references/issues-log.md

## 2025-10-30: 테스트 실패
문제: ...
원인: ...
해결: ...
```

### Orchestrator 문의

리팩터링 보고서의 "질문 사항" 섹션 활용

---

## 🎯 성공 기준

✅ **테스트 100% 통과 유지** (최우선)  
✅ **코드 품질 개선** (DRY, 타입, 가독성)  
✅ **문서화 완료** (변경 근거, 영향 분석)

**다음 단계:** Phase 6 (VALIDATE)

---

**마지막 업데이트**: 2025-10-30  
**버전**: 1.0.0  
**Phase**: 5 (REFACTOR)
