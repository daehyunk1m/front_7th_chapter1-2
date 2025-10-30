# Quick Start: 테스트 전략 사용 가이드

**대상**: test-writer 에이전트 (Phase 3)
**목표**: 이 전략을 기반으로 테스트 파일 작성

---

## 🎯 한눈에 보기

### 작성할 테스트 파일 (4개)

```
src/__tests__/
├── unit/
│   ├── task.repeatUtils.spec.ts          ← 35개 케이스
│   └── easy.eventOverlap.spec.ts (수정)  ← +3개
├── hooks/
│   └── task.useEventOperations.spec.ts   ← 16개 케이스
└── integration/
    └── task.repeat-event-integration.spec.ts ← 13개 케이스
```

**총 케이스**: 67개 이상

---

## 📖 문서 읽기 순서

### 1단계: 개요 파악 (5분)

- 이 문서 (QUICK_START.md)
- getting-started.md의 "테스트 전략 핵심 요약"

### 2단계: 상세 내용 학습 (30분)

- `2025-10-30_test-strategy-summary.md` 읽기
- "테스트 케이스 상세 계획" 섹션 확인

### 3단계: 상세 명세 확인 (1시간)

- `2025-10-30_repeat-event-test-strategy.md` 읽기
- 각 테스트 케이스별로 섹션 2.1, 2.2, 2.3 참고
- MSW 핸들러 전략 (섹션 3)
- 테스트 데이터 (섹션 4)

### 4단계: 기존 테스트 패턴 분석 (20분)

- `src/__tests__/unit/easy.dateUtils.spec.ts` 참고
- `src/__tests__/hooks/medium.useEventOperations.spec.ts` 참고
- GWT 패턴과 명명 규칙 학습

---

## 🔑 핵심 포인트 (반드시 읽기)

### 1. 테스트 분류 기준

| 파일명 패턴        | 목적                          | 예시                                |
| ------------------ | ----------------------------- | ----------------------------------- |
| `task.*.spec.ts`   | **새로 작성하는 테스트**      | `task.repeatUtils.spec.ts`          |
| `easy.*.spec.ts`   | 단순 유틸 테스트 (기존)       | `easy.dateUtils.spec.ts`            |
| `medium.*.spec.ts` | 복잡한 로직/API 테스트 (기존) | `medium.useEventOperations.spec.ts` |

**중요**: "task"로 시작하는 파일을 작성하세요 (새로운 기능이므로)

### 2. 테스트 이름 규칙

**한글로 작성하되 구체적이어야 함**:

```typescript
// ✅ 좋은 예
it('매월 31일 반복 시 31일이 있는 달에만 생성되어야 한다', () => {});

// ❌ 나쁜 예
it('31일 처리', () => {});
```

### 3. GWT 패턴 (모든 테스트에 필수)

```typescript
it('구체적인 시나리오 설명', () => {
  // Given: 테스트 데이터 준비
  const input = { type: 'monthly', interval: 1, endDate: '2025-12-31' };

  // When: 테스트 대상 함수 호출
  const result = generateRepeatDates('2025-01-31', input);

  // Then: 결과 검증
  expect(result).toHaveLength(7);
});
```

---

## 📋 체크리스트: 테스트 작성 전

### 개발 환경 확인

- [ ] `pnpm` 설치됨
- [ ] `pnpm install` 완료
- [ ] `pnpm test:ui` 실행 가능

### 패턴 학습

- [ ] 기존 테스트 파일 읽음 (easy._, medium._)
- [ ] GWT 패턴 이해함
- [ ] MSW 핸들러 방식 이해함

### 문서 준비

- [ ] 전략 문서 읽음 (이 파일들)
- [ ] 테스트 케이스 목록 인쇄/확인
- [ ] 특수 규칙 (31일, 윤년) 이해함

---

## 🛠️ 파일별 작성 가이드

### 1. `task.repeatUtils.spec.ts` (35개 케이스)

**작성 순서**:

1. describe 블록 4개 생성
   - `generateRepeatDates` (15개)
   - `getNextRepeatDate` (7개)
   - `isValid31stMonthly` (5개)
   - `isValidLeapYearFeb29` (5개)
2. `formatRepeatInfo` describe 블록 추가 (3개)
3. 각 케이스별 it() 블록 작성

**참고 섹션**: `2025-10-30_repeat-event-test-strategy.md`의 **섹션 2.1**

**주의사항**:

- `generateRepeatDates`의 15번 케이스는 가장 복잡함
- 31일 특수 규칙과 윤년 규칙이 혼합될 수 있음
- Edge case (빈 배열, null, 유효하지 않은 입력) 필수

### 2. `task.useEventOperations.spec.ts` (16개 케이스)

**작성 순서**:

1. MSW 모킹 설정
2. describe 블록 4개 생성
   - `saveRecurringEvents` (5개)
   - `updateRecurringSeries` (4개)
   - `deleteRecurringSeries` (4개)
   - `saveEvent 분기 로직` (3개)

**참고 섹션**: `2025-10-30_repeat-event-test-strategy.md`의 **섹션 2.2** 및 **섹션 3 (MSW)**

**주의사항**:

- MSW 핸들러 추가 필요 (3개: POST /api/events-list, PUT/DELETE /api/recurring-events/:repeatId)
- notistack mocking 필수
- `renderHook()` 와 `act()` 사용

**참고 파일**:

```typescript
// medium.useEventOperations.spec.ts 의 패턴 참고
```

### 3. `task.repeat-event-integration.spec.ts` (13개 케이스)

**작성 순서**:

1. describe 블록 4개 생성
   - `반복 일정 전체 흐름 - E2E` (4개)
   - `반복 일정 특수 규칙` (3개)
   - `반복 일정과 일정 겹침 감지` (3개)
   - `반복 일정 에러 처리` (3개)

**참고 섹션**: `2025-10-30_repeat-event-test-strategy.md`의 **섹션 2.3**

**주의사항**:

- 전체 흐름 테스트 (생성 → 표시 → 수정 → 삭제)
- API 분기 로직 검증 필수
- `findOverlappingEvents` 함수와의 상호작용 검증

### 4. `easy.eventOverlap.spec.ts` 수정 (3개 추가)

**작성 순서**:

1. 기존 테스트 유지
2. 새 describe 블록 추가: `반복 일정 제외 검증`
3. 3개 케이스 추가

**참고 섹션**: `2025-10-30_repeat-event-test-strategy.md`의 **섹션 2.3.3**

**주의사항**:

- 기존 테스트는 모두 단일 일정(repeat.type='none')이므로 변경 없음
- 새로운 케이스만 추가

---

## 🧪 테스트 실행 및 확인

### 작성 후 실행

```bash
# 모든 새 테스트 실행 (RED 상태 예상)
pnpm test

# 또는 특정 파일만 테스트
pnpm test -- task.repeatUtils.spec.ts
pnpm test -- task.useEventOperations.spec.ts
pnpm test -- task.repeat-event-integration.spec.ts

# UI 모드로 시각적 확인
pnpm test:ui
```

### 예상 결과

```
FAIL src/__tests__/unit/task.repeatUtils.spec.ts
FAIL src/__tests__/hooks/task.useEventOperations.spec.ts
FAIL src/__tests__/integration/task.repeat-event-integration.spec.ts

Tests:  0 passed, 67 failed, 67 total
```

**이것이 정상입니다** (구현 코드가 아직 없으므로)

---

## 📊 특수 규칙 테스트 예제

### 31일 매월 반복 예제

```typescript
it('매월 31일 반복 시 31일이 있는 달에만 생성된다', () => {
  // Given
  const baseDate = '2025-01-31';
  const repeatInfo = {
    type: 'monthly',
    interval: 1,
    endDate: '2025-12-31',
  };

  // When
  const result = generateRepeatDates(baseDate, repeatInfo);

  // Then
  // 31일이 있는 달: 1, 3, 5, 7, 8, 10, 12월 (7개)
  expect(result).toHaveLength(7);
  expect(result).toEqual([
    '2025-01-31',
    '2025-03-31',
    '2025-05-31',
    '2025-07-31',
    '2025-08-31',
    '2025-10-31',
    '2025-12-31',
  ]);
});
```

### 윤년 2월 29일 반복 예제

```typescript
it('매년 2월 29일 반복 시 윤년에만 생성된다', () => {
  // Given
  const baseDate = '2024-02-29';
  const repeatInfo = {
    type: 'yearly',
    interval: 1,
    endDate: '2032-02-29',
  };

  // When
  const result = generateRepeatDates(baseDate, repeatInfo);

  // Then
  // 윤년: 2024, 2028, 2032 (3개)
  // 평년 제외: 2025, 2026, 2027, 2029, 2030, 2031
  expect(result).toHaveLength(3);
  expect(result).toEqual(['2024-02-29', '2028-02-29', '2032-02-29']);
});
```

---

## 🔗 관련 파일 위치

### 구현되어야 할 파일들 (참고용)

```
src/
├── types.ts                     ← 배치 API 타입 추가
├── utils/
│   ├── repeatUtils.ts           ← 새로 만들 파일
│   └── eventOverlap.ts          ← 수정할 파일
├── hooks/
│   └── useEventOperations.ts    ← 수정할 파일
├── __mocks__/
│   └── handlers.ts              ← MSW 핸들러 추가
└── App.tsx                       ← UI 통합
```

### 기존 테스트 참고

```
src/__tests__/
├── unit/
│   ├── easy.dateUtils.spec.ts        ← 순수 함수 패턴
│   └── easy.eventOverlap.spec.ts
├── hooks/
│   ├── medium.useEventOperations.spec.ts ← 훅 + MSW 패턴
│   └── medium.useNotifications.spec.ts
└── integration/                      ← 아직 없음 (새로 만들기)
```

---

## ⚠️ 주의사항

### 1. 파일명 규칙

- `task.` 접두어 사용 (새로운 테스트)
- `easy.` 또는 `medium.` 기존 테스트 수정 시에만 사용

### 2. 한글 사용

- 테스트 이름: **한글 필수**
- describe 블록: **한글 권장**
- 주석: **한글 권장**

### 3. GWT 패턴

- 모든 테스트가 Given-When-Then 구조 필수
- 주석으로 명시 필수

### 4. 테스트 데이터

- 고정된 날짜 사용 (현재 날짜 기반 계산 금지)
- 특수 규칙 (31일, 윤년) 모두 포함
- Edge case (빈 배열, null, 유효하지 않은 입력) 포함

### 5. MSW 핸들러

- 3개 신규 핸들러 필요:
  - `POST /api/events-list` (배치 생성)
  - `PUT /api/recurring-events/:repeatId` (시리즈 수정)
  - `DELETE /api/recurring-events/:repeatId` (시리즈 삭제)

---

## 📞 문제 발생 시

### 문제: 테스트 케이스가 명확하지 않을 때

**해결**: `2025-10-30_repeat-event-test-strategy.md`의 해당 섹션 다시 읽기

### 문제: MSW 핸들러 작성 방법 모르겠을 때

**해결**:

- `src/__mocks__/handlers.ts` 기존 핸들러 참고
- 섹션 3 "MSW 핸들러 전략" 읽기

### 문제: 테스트 실행 시 에러 발생

**해결**:

1. 테스트 파일 문법 확인 (TypeScript 컴파일)
2. import 경로 확인
3. `pnpm test --reporter=verbose` 로 상세 메시지 확인

### 문제: GWT 패턴 적용 방법 모르겠을 때

**해결**: 섹션 6 "GWT 패턴 적용 가이드" 읽기

---

## ✅ 최종 체크리스트

테스트 파일 작성 완료 후:

- [ ] 모든 테스트 파일이 `task.` 접두어로 시작
- [ ] 모든 테스트 이름이 한글
- [ ] 모든 테스트가 GWT 패턴 적용
- [ ] 67개 이상 테스트 케이스 작성
- [ ] `pnpm test` 실행 시 모든 테스트 RED (실패)
- [ ] `pnpm lint` 에러 없음
- [ ] TypeScript 컴파일 에러 없음

---

## 🚀 다음 단계

작성 완료 후:

1. 모든 테스트 파일을 git에 커밋
2. `pnpm test` 실행하여 RED 상태 확인
3. 이 결과를 code-writer에게 전달
4. code-writer가 구현을 진행

---

**최종 업데이트**: 2025-10-30
**예상 작업 시간**: 약 2시간 (67개 케이스)
**다음 단계**: Phase 4 - code-writer가 구현
