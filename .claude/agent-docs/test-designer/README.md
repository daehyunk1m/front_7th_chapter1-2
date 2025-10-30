# Test Designer Agent - 반복 일정 기능 테스트 전략

**Phase**: Phase 2 - Test Design
**상태**: 완료
**작성일**: 2025-10-30

---

## 📁 문서 구조

### 핵심 문서

#### 1. 테스트 전략 상세 문서
**파일**: `logs/2025-10-30_repeat-event-test-strategy.md` (1,114줄)

반복 일정 기능에 대한 완전한 테스트 전략 명세:
- 테스트 케이스 67개+ 상세 설계
- MSW 핸들러 전략 (3개 신규 엔드포인트)
- 테스트 데이터 및 픽스처
- 커버리지 목표 및 실행 계획
- GWT 패턴 적용 가이드
- 엣지 케이스 및 특수 테스트 전략

**대상 독자**: test-writer 에이전트 (Phase 3)

#### 2. 테스트 전략 요약 문서
**파일**: `logs/TEST_STRATEGY_SUMMARY.md` (300줄)

테스트 전략의 핵심 요약:
- 테스트 계획 개요 (67개+ 케이스)
- 테스트 파일별 구성 (단위/훅/통합)
- 커버리지 목표 (80% 이상)
- Phase별 실행 순서
- 검증 완료 항목

**대상 독자**: 프로젝트 리더, orchestrator

---

## 🎯 테스트 전략 핵심 요약

### 테스트 규모

| 항목 | 개수 | 파일 |
|-----|------|------|
| 단위 테스트 | 35+ | `task.repeatUtils.spec.ts` |
| 훅 테스트 | 16+ | `task.useEventOperations.spec.ts` |
| 통합 테스트 | 13+ | `task.repeat-event-integration.spec.ts` |
| 추가 테스트 | 3 | `easy.eventOverlap.spec.ts` (수정) |
| **총계** | **67+** | **4 파일** |

### 테스트 케이스 분포

```
generateRepeatDates (15개)
├─ 매일 반복
├─ 매주 반복
├─ 매월 반복 (31일 특수 규칙)
├─ 매년 반복 (윤년 규칙)
└─ 예외 처리

getNextRepeatDate (7개)
├─ 매일/주/월/년 계산
├─ 다양한 간격
└─ 예외 처리

isValid31stMonthly (5개)
├─ 31일 있는 달
├─ 31일 없는 달
└─ 다양한 일자

isValidLeapYearFeb29 (5개)
├─ 윤년 (2024, 2000)
├─ 평년 (2025, 1900)
└─ 100년/400년 경계

헬퍼 함수 (3개)
├─ formatRepeatInfo
└─ isLeapYear 등

API 통신 (16개)
├─ saveRecurringEvents (5개)
├─ updateRecurringSeries (4개)
├─ deleteRecurringSeries (4개)
├─ saveEvent 분기 로직 (3개)

E2E & 통합 (13개)
├─ 완전한 흐름 (4개)
├─ 특수 규칙 (3개)
├─ 겹침 제외 (3개)
├─ 에러 시나리오 (3개)

겹침 제외 (3개)
├─ 반복 일정 제외
├─ 일반 일정 겹침
└─ 반복 일정 겹침
```

### 커버리지 목표

- **전체**: 80% 이상
- **핵심 함수**: 100%
  - `generateRepeatDates`: 100%
  - `getNextRepeatDate`: 100%
  - `isValid31stMonthly`: 100%
  - `isValidLeapYearFeb29`: 100%
- **API 훅**: 90% 이상
- **겹침 로직**: 90% 이상

---

## 🔧 기술 사양

### 추가되는 MSW 핸들러 (3개)

```typescript
// 1. 배치 생성
POST /api/events-list
요청: { events: EventForm[] }
응답: { events: Event[] } (repeatId 포함)

// 2. 시리즈 수정
PUT /api/recurring-events/:repeatId
요청: UpdateRecurringSeriesRequest
응답: 200 또는 404

// 3. 시리즈 삭제
DELETE /api/recurring-events/:repeatId
요청: (본문 없음)
응답: 204 또는 404
```

### 테스트 데이터 픽스처

**반복 유형별 샘플**:
- 매일: 5일 반복 (1월 15-20일)
- 매주: 5주 반복 (1월 15 - 2월 15일)
- 매월 31일: 7개월 (1,3,5,7,8,10,12월)
- 매년 2월 29일: 4년 주기 (2024,2028,2032)

### GWT 패턴 적용

모든 테스트는 3단계 구조:
```typescript
it('한글 시나리오 설명', () => {
  // Given: 테스트 환경 준비
  // When: 동작 실행
  // Then: 결과 검증
});
```

---

## 📋 특수 규칙 테스트 전략

### 1. 31일 매월 반복

**규칙**: 31일이 있는 달(1,3,5,7,8,10,12월)에만 생성

**테스트 매트릭스**:
| 기준월 | 반복 종료 | 예상 개수 | 포함 월 |
|--------|---------|---------|--------|
| 2025-01 | 2025-12 | 7 | 1,3,5,7,8,10,12 |
| 2024-01 | 2024-12 | 7 | (동일) |

**테스트 케이스**:
- ✓ 매월 31일 반복 (기본)
- ✓ 매월 30일 반복 (2월 건너뜀)
- ✓ 다양한 시작월 (모두 31일 있는 달만)

### 2. 윤년 2월 29일 반복

**규칙**: 윤년의 2월 29일에만 생성 (4년 주기, 100년/400년 예외)

**테스트 매트릭스**:
| 기준연도 | 반복 종료 | 예상 개수 | 포함 연도 |
|---------|---------|---------|---------|
| 2024 | 2032 | 3 | 2024,2028,2032 |
| 2000 | 2008 | 3 | 2000,2004,2008 |

**테스트 케이스**:
- ✓ 표준 윤년 (4년 주기)
- ✓ 100년 경계 (1900은 평년, 2000은 윤년)
- ✓ 400년 예외 처리

---

## 🚀 실행 계획

### Phase 3: RED - Test Writing (test-writer)
**기간**: 약 2시간
**산출물**:
1. `src/__tests__/unit/task.repeatUtils.spec.ts` (35개 케이스)
2. `src/__tests__/hooks/task.useEventOperations.spec.ts` (16개 케이스)
3. `src/__tests__/integration/task.repeat-event-integration.spec.ts` (13개 케이스)
4. `src/__tests__/unit/easy.eventOverlap.spec.ts` (추가 3개)

**성공 기준**:
```bash
pnpm test
# 결과: 0 passed, 67 failed (예상)
```

### Phase 4: GREEN - Implementation (code-writer)
**기간**: 약 2.5시간
**구현 순서**:
1. `src/types.ts` - 배치 API 타입 추가
2. `src/utils/repeatUtils.ts` - 유틸 함수 구현
3. `src/utils/eventOverlap.ts` - 겹침 제외 로직
4. `src/hooks/useEventOperations.ts` - 배치 API 메서드
5. `src/App.tsx` - UI 통합

**성공 기준**:
```bash
pnpm test
# 결과: 67 passed, 0 failed
pnpm test:coverage
# 결과: 80% 이상 커버리지
```

### Phase 5-6: REFACTOR & VALIDATE
**기간**: 약 2시간
**활동**:
- 코드 품질 개선
- 최종 검증 및 보고서

---

## 📚 문서 네비게이션

### 상세 문서 (phase-specific)
- [Feature Spec](../feature-designer/logs/2025-10-30_repeat-event-spec.md) - 기능 명세
- [Work Plan](../orchestrator/logs/2025-10-30_repeat-event-plan.md) - 전체 작업 계획
- **[Test Strategy](./logs/2025-10-30_repeat-event-test-strategy.md)** - 테스트 전략 ← **현재 문서**

### 참고 자료
- [CLAUDE.md](../../CLAUDE.md) - 프로젝트 규칙
- 기존 테스트: `src/__tests__/` 폴더의 `easy.*` 및 `medium.*` 테스트

---

## ✅ 품질 체크리스트

### 테스트 설계 완료 항목
- [x] 67개+ 테스트 케이스 설계
- [x] GWT 패턴 정의
- [x] 특수 규칙(31일, 윤년) 포함
- [x] 엣지 케이스 식별
- [x] MSW 핸들러 명세
- [x] 테스트 데이터 픽스처
- [x] 커버리지 목표 설정
- [x] 단계별 실행 계획

### 다음 담당자 (test-writer) 확인사항
- [ ] 테스트 파일 67개+ 작성
- [ ] 모든 테스트 RED 상태 확인
- [ ] 테스트 문법 에러 없음
- [ ] 테스트 이름 한글 작성
- [ ] GWT 패턴 적용

---

## 📞 연락처 및 추가 질문

**현재 담당**: test-designer agent
**다음 담당**: test-writer agent
**기술 문제**: code-writer agent

각 문서의 끝에 **추가 질문** 섹션을 참고하세요.

---

**최종 업데이트**: 2025-10-30
**상태**: ✅ Phase 2 완료, Phase 3 대기 중
**다음 단계**: test-writer 에이전트 호출 (Phase 3: RED)
