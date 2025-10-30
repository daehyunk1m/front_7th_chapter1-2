# Work Plan: 반복 일정 기능 추가

**작성일**: 2025-10-30
**작업 ID**: repeat-event
**목표**: 반복 일정(daily/weekly/monthly/yearly) 생성 및 관리 기능 구현

---

## 📋 개요

### 작업 목표
- 사용자가 일정 생성 시 반복 유형(없음/매일/매주/매월/매년)을 선택할 수 있도록 기능 추가
- 반복 일정 시리즈 생성 시 배치 API 활용
- 특수 규칙 구현: 31일 매월 반복, 2월 29일 매년 반복
- 반복 일정은 일정 겹침 감지 제외
- 반복 시리즈 전체 수정/삭제 기능 지원

### 범위
1. **Backend (이미 구현됨)**: 배치 API 엔드포인트 사용 (`/api/events-list`, `/api/recurring-events/:repeatId`)
2. **Types**: RepeatInfo 인터페이스 활성화 및 타입 시스템 확장
3. **Utils**: 반복 일정 생성 로직 (순수 함수)
4. **Hooks**: useEventOperations에 배치 API 통합
5. **UI**: App.tsx에 반복 설정 폼 추가 (주석 해제 및 개선)
6. **Tests**: 타입, 유틸, 훅, 통합 테스트

### 예상 소요 시간
- **전체**: 8-10시간
- Phase 0 (Planning): 1시간 ✅
- Phase 1 (Feature Design): 1.5시간
- Phase 2 (Test Design): 1시간
- Phase 3 (RED): 2시간
- Phase 4 (GREEN): 2.5시간
- Phase 5 (REFACTOR): 1시간
- Phase 6 (VALIDATE): 1시간

---

## 🔨 작업 세분화

### 1. 타입 시스템 업데이트 (우선순위: P0)

**범위**:
- RepeatInfo 인터페이스 검증 (이미 정의됨)
- EventForm, Event 타입에 repeat 필드 활성화 확인
- 배치 API 요청/응답 타입 추가

**의존성**: 없음

**담당 에이전트**: feature-designer (Phase 1)

**결과물**:
- `src/types.ts` (RepeatInfo 활성화 확인 및 배치 API 타입 추가)
- 기술 명세서: `.claude/agent-docs/feature-designer/logs/2025-10-30_repeat-event-spec.md`

**영향 파일**:
- `src/types.ts`

**복잡도**: Low (타입은 이미 정의되어 있음)

---

### 2. 반복 일정 생성 유틸리티 (우선순위: P0)

**범위**:
- 순수 함수로 반복 일정 날짜 계산
- 특수 규칙 구현: 31일 매월, 2월 29일 매년
- RepeatType별 로직 분리

**의존성**: 타입 시스템 (Task 1)

**담당 에이전트**:
- test-designer (Phase 2): 테스트 전략 수립
- test-writer (Phase 3): 테스트 작성
- code-writer (Phase 4): 유틸 구현

**결과물**:
- `src/utils/repeatUtils.ts` (신규)
- `src/__tests__/unit/task.repeatUtils.spec.ts` (신규)

**영향 파일**:
- `src/utils/repeatUtils.ts` (신규)
- `src/__tests__/unit/task.repeatUtils.spec.ts` (신규)

**복잡도**: Medium

**핵심 함수**:
```typescript
// 반복 일정 날짜 배열 생성
generateRepeatDates(baseDate: string, repeatInfo: RepeatInfo): string[]

// 특정 반복 유형의 다음 날짜 계산
getNextRepeatDate(currentDate: string, repeatType: RepeatType, interval: number): string | null

// 31일 매월 규칙 검증
isValid31stMonthly(date: string): boolean

// 윤년 2월 29일 규칙 검증
isValidLeapYearFeb29(date: string): boolean
```

---

### 3. 일정 겹침 감지 수정 (우선순위: P1)

**범위**:
- 반복 일정은 겹침 감지에서 제외
- `findOverlappingEvents` 함수 수정

**의존성**: 타입 시스템 (Task 1)

**담당 에이전트**:
- test-writer (Phase 3): 테스트 케이스 추가
- code-writer (Phase 4): 로직 수정

**결과물**:
- `src/utils/eventOverlap.ts` (수정)
- `src/__tests__/unit/easy.eventOverlap.spec.ts` (케이스 추가)

**영향 파일**:
- `src/utils/eventOverlap.ts`
- `src/__tests__/unit/easy.eventOverlap.spec.ts`

**복잡도**: Low

---

### 4. useEventOperations 훅 확장 (우선순위: P0)

**범위**:
- 배치 API 통합 (`POST /api/events-list`)
- 반복 시리즈 수정/삭제 (`PUT/DELETE /api/recurring-events/:repeatId`)
- 반복 일정 저장 시 단일 vs 배치 API 선택 로직

**의존성**:
- Task 1 (타입)
- Task 2 (유틸)

**담당 에이전트**:
- test-designer (Phase 2): API 통합 테스트 전략
- test-writer (Phase 3): MSW 핸들러 및 테스트 작성
- code-writer (Phase 4): 훅 구현

**결과물**:
- `src/hooks/useEventOperations.ts` (수정)
- `src/__tests__/hooks/task.useEventOperations.spec.ts` (신규)

**영향 파일**:
- `src/hooks/useEventOperations.ts`
- `src/__tests__/hooks/task.useEventOperations.spec.ts` (신규)

**복잡도**: High

**핵심 API 메서드**:
```typescript
// 반복 일정 저장 (배치)
saveRecurringEvents(eventForm: EventForm): Promise<void>

// 반복 시리즈 전체 수정
updateRecurringSeries(repeatId: string, updateData: Partial<EventForm>): Promise<void>

// 반복 시리즈 전체 삭제
deleteRecurringSeries(repeatId: string): Promise<void>
```

---

### 5. UI 통합 - 반복 설정 폼 (우선순위: P1)

**범위**:
- App.tsx의 주석 처리된 반복 UI 활성화
- RepeatType 선택 드롭다운
- 반복 간격 입력
- 반복 종료일 입력
- 반복 일정 편집 시 "이 일정만 / 전체 시리즈" 옵션

**의존성**:
- Task 1 (타입)
- Task 4 (훅)

**담당 에이전트**:
- code-writer (Phase 4): UI 구현

**결과물**:
- `src/App.tsx` (수정)

**영향 파일**:
- `src/App.tsx`

**복잡도**: Medium

---

### 6. UI 통합 - 반복 일정 표시 (우선순위: P2)

**범위**:
- 달력 뷰에서 반복 일정 시각적 구분 (아이콘 추가)
- 일정 목록에서 반복 정보 표시
- 반복 시리즈 편집/삭제 시 확인 다이얼로그

**의존성**: Task 5 (UI 폼)

**담당 에이전트**:
- code-writer (Phase 4): UI 개선

**결과물**:
- `src/App.tsx` (수정)

**영향 파일**:
- `src/App.tsx`

**복잡도**: Low

---

### 7. 통합 테스트 (우선순위: P0)

**범위**:
- E2E 시나리오: 반복 일정 생성 → 표시 → 수정 → 삭제
- 특수 규칙 시나리오: 31일, 윤년
- 일정 겹침 제외 검증

**의존성**: 모든 이전 작업

**담당 에이전트**:
- test-designer (Phase 2): 통합 테스트 시나리오 설계
- test-writer (Phase 3): 통합 테스트 작성
- refactoring-expert (Phase 5): 테스트 리팩토링

**결과물**:
- `src/__tests__/integration/task.repeat-event-integration.spec.ts` (신규)

**영향 파일**:
- `src/__tests__/integration/task.repeat-event-integration.spec.ts` (신규)

**복잡도**: High

---

## 📊 실행 순서

### Foundation Layer (Phase 1-3)
1. **Phase 1: Feature Design**
   - 모든 타입, 유틸, 훅, UI 설계 완료
   - 기술 명세서 작성

2. **Phase 2: Test Design**
   - 유닛 테스트 전략 (repeatUtils, eventOverlap)
   - 통합 테스트 전략 (useEventOperations)
   - E2E 테스트 시나리오

3. **Phase 3: RED - Test Writing**
   - `task.repeatUtils.spec.ts` 작성
   - `task.useEventOperations.spec.ts` 작성
   - `task.repeat-event-integration.spec.ts` 작성
   - 기존 테스트 케이스 추가 (`easy.eventOverlap.spec.ts`)
   - 모든 테스트 실행 결과: FAIL (예상됨)

### Implementation Layer (Phase 4)
4. **Phase 4: GREEN - Implementation**
   - 실행 순서 (의존성 순):
     1. `src/types.ts` 확인/수정
     2. `src/utils/repeatUtils.ts` 구현
     3. `src/utils/eventOverlap.ts` 수정
     4. `src/hooks/useEventOperations.ts` 확장
     5. `src/App.tsx` UI 통합
   - 각 단계마다 해당 테스트 통과 확인
   - 최종: 모든 테스트 통과

### Quality Layer (Phase 5-6)
5. **Phase 5: REFACTOR**
   - 코드 중복 제거
   - 함수 분리 및 명명 개선
   - 성능 최적화 (필요 시)
   - 테스트 유지 확인

6. **Phase 6: VALIDATE**
   - 전체 테스트 스위트 실행
   - 커버리지 검증 (목표: 80% 이상)
   - TypeScript/ESLint 검증
   - 수동 테스트 시나리오 실행
   - 최종 검증 보고서 작성

---

## ✅ 품질 검증 포인트

### Phase 1 검증 (Feature Design)
- [ ] RepeatInfo 타입 정의 확인됨
- [ ] 배치 API 인터페이스 설계 완료
- [ ] 유틸 함수 시그니처 정의
- [ ] 훅 인터페이스 설계
- [ ] UI 컴포넌트 구조 명확
- [ ] 특수 규칙 명세 명확

### Phase 2 검증 (Test Design)
- [ ] 테스트 케이스 목록 완성 (최소 20개)
- [ ] GWT 패턴 적용 계획
- [ ] MSW 핸들러 전략 수립
- [ ] 커버리지 목표 설정 (80%)
- [ ] Edge case 식별 완료

### Phase 3 검증 (RED)
- [ ] 모든 테스트 파일 생성됨
- [ ] 테스트 실행 결과: 모두 실패 (RED)
- [ ] 구현 코드 없음 (utils, hooks)
- [ ] 실패 메시지 명확
- [ ] MSW 핸들러 준비됨

### Phase 4 검증 (GREEN)
- [ ] 모든 테스트 통과 (GREEN)
- [ ] TypeScript 컴파일 성공
- [ ] 기존 테스트 영향 없음
- [ ] API 통신 정상 작동
- [ ] UI 기능 동작 확인

### Phase 5 검증 (REFACTOR)
- [ ] 코드 품질 개선 완료
- [ ] 테스트 여전히 통과
- [ ] ESLint 검사 통과
- [ ] 성능 최적화 적용 (필요 시)
- [ ] 코드 리뷰 준비 완료

### Phase 6 검증 (VALIDATE)
- [ ] 전체 테스트 통과율: 100%
- [ ] 커버리지: 80% 이상
- [ ] TypeScript 에러: 0
- [ ] ESLint 에러: 0
- [ ] 수동 테스트 시나리오 통과
- [ ] 최종 보고서 작성 완료

---

## ⚠️ 리스크 평가

### 높은 위험 (High Risk)

**1. 특수 규칙 구현의 복잡성**
- **문제**: 31일 매월, 윤년 2월 29일 규칙의 Edge case
- **영향**: 날짜 계산 오류, 사용자 혼란
- **대응 방안**:
  - 상세한 테스트 케이스 작성 (연도별, 월별)
  - 날짜 라이브러리 활용 검토 (필요 시)
  - 사용자에게 명확한 안내 메시지

**2. 배치 API 통합**
- **문제**: 여러 일정 생성 시 성능 및 에러 처리
- **영향**: 사용자 경험 저하, 데이터 불일치
- **대응 방안**:
  - 트랜잭션 방식 에러 처리 (전체 성공 or 전체 실패)
  - 로딩 인디케이터 추가
  - 실패 시 명확한 에러 메시지

### 중간 위험 (Medium Risk)

**3. 기존 코드와의 통합**
- **문제**: App.tsx가 661줄로 큼, 복잡도 증가
- **영향**: 유지보수성 저하, 버그 위험
- **대응 방안**:
  - 최소 변경 원칙 준수
  - 반복 관련 로직은 훅으로 분리
  - 리팩토링은 Phase 5에서 처리

**4. 일정 겹침 감지 제외**
- **문제**: 반복 일정만 제외 로직의 정확성
- **영향**: 일반 일정 겹침 감지 영향
- **대응 방안**:
  - 기존 테스트 케이스 유지
  - 반복 일정 전용 테스트 추가
  - 회귀 테스트 강화

### 낮은 위험 (Low Risk)

**5. UI 변경**
- **문제**: 사용자 인터페이스 혼란
- **영향**: 사용성 저하
- **대응 방안**:
  - 기존 UI 패턴 유지
  - 명확한 라벨 및 도움말
  - 접근성 속성 추가

---

## 📌 참조 문서

### 프로젝트 규칙
- [CLAUDE.md](../../../CLAUDE.md) - 프로젝트 전체 규칙
- [folder-tree.md](../../docs/folder-tree.md) - 폴더 구조

### 에이전트 정의
- [orchestrator.md](../../agents/orchestrator.md) - 조율자 역할
- [feature-designer.md](../../agents/feature-designer.md) - 설계 담당
- [test-designer.md](../../agents/test-designer.md) - 테스트 전략
- [test-writer.md](../../agents/test-writer.md) - 테스트 작성
- [code-writer.md](../../agents/code-writer.md) - 구현 담당
- [refactoring-expert.md](../../agents/refactoring-expert.md) - 리팩토링

### 관련 코드 파일
- `src/types.ts` - 타입 정의
- `src/hooks/useEventOperations.ts` - API 통신 패턴
- `src/hooks/useEventForm.ts` - 폼 상태 관리
- `src/utils/dateUtils.ts` - 날짜 유틸 참고
- `src/utils/eventOverlap.ts` - 겹침 감지
- `src/App.tsx` - 메인 애플리케이션
- `server.js` - 백엔드 API (배치 엔드포인트 확인)

### 테스트 참고
- `src/__tests__/unit/easy.dateUtils.spec.ts` - 순수 함수 테스트 예시
- `src/__tests__/hooks/medium.useEventOperations.spec.ts` - 훅 테스트 예시
- `src/__tests__/unit/easy.eventOverlap.spec.ts` - 겹침 감지 테스트

---

## 🎯 성공 기준

### 기능 요구사항
- ✅ 사용자가 반복 유형 선택 가능 (없음/매일/매주/매월/매년)
- ✅ 반복 간격 및 종료일 설정 가능
- ✅ 31일 매월 반복: 31일이 있는 달에만 생성
- ✅ 윤년 2월 29일 매년 반복: 2월 29일에만 생성
- ✅ 반복 일정은 일정 겹침 감지 제외
- ✅ 반복 시리즈 전체 수정/삭제 가능
- ✅ 반복 일정 시각적 구분 (아이콘)

### 기술 요구사항
- ✅ 배치 API 사용 (`/api/events-list`, `/api/recurring-events/:repeatId`)
- ✅ 타입 안전성 보장 (TypeScript)
- ✅ 순수 함수로 유틸 작성
- ✅ 기존 코드 패턴 준수
- ✅ 테스트 커버리지 80% 이상

### 품질 요구사항
- ✅ 모든 테스트 통과
- ✅ TypeScript 컴파일 에러 없음
- ✅ ESLint 에러/경고 없음
- ✅ 기존 기능 영향 없음 (회귀 테스트)
- ✅ 코드 리뷰 준비 완료

---

## 📅 Phase별 예상 일정

| Phase | 설명 | 예상 시간 | 담당 | 산출물 |
|-------|------|-----------|------|--------|
| Phase 0 | Planning | 1시간 | orchestrator | 이 문서 |
| Phase 1 | Feature Design | 1.5시간 | feature-designer | spec.md |
| Phase 2 | Test Design | 1시간 | test-designer | test-strategy.md |
| Phase 3 | RED | 2시간 | test-writer | *.spec.ts (실패) |
| Phase 4 | GREEN | 2.5시간 | code-writer | 구현 코드 (통과) |
| Phase 5 | REFACTOR | 1시간 | refactoring-expert | 개선된 코드 |
| Phase 6 | VALIDATE | 1시간 | orchestrator | final-report.md |
| **총계** | | **10시간** | | |

---

## 🔄 다음 단계

### 즉시 실행
1. ✅ Work Plan 작성 완료 (이 문서)
2. Feature 브랜치 생성: `git checkout -b feat/repeat-event`
3. Phase 0 커밋 및 태그
4. Phase 1 Handoff 문서 생성
5. feature-designer 에이전트 호출

### Phase 1 준비사항
- handoff/phase1.md 작성
- feature-designer에게 전달할 컨텍스트 정리
- 설계 요구사항 명확화

---

**최종 업데이트**: 2025-10-30
**버전**: 1.0.0
**상태**: Phase 0 완료, Phase 1 준비 중
