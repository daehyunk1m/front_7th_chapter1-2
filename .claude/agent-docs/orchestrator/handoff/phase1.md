---
phase: 1
agent: feature-designer
timestamp: 2025-10-30T10:00:00Z
status: ready

inputs:
  requirement: "반복 일정 기능 추가 - 사용자가 daily/weekly/monthly/yearly 반복 유형을 선택할 수 있고, 특수 규칙(31일 매월, 윤년 2월 29일)을 지원"
  context_files:
    - ../../../CLAUDE.md
    - ../orchestrator/logs/2025-10-30_repeat-event-plan.md
    - ../../../src/types.ts
    - ../../../src/hooks/useEventOperations.ts
    - ../../../src/hooks/useEventForm.ts
    - ../../../src/utils/dateUtils.ts
    - ../../../server.js

references:
  agent_definition: ../../agents/feature-designer.md
  agent_prompt: ../feature-designer/prompt.md
  shared_docs:
    - ../../docs/folder-tree.md
    - ../../docs/rule-of-make-good-test.md

output_requirements:
  path: .claude/agent-docs/feature-designer/logs/2025-10-30_repeat-event-spec.md
  required_sections:
    - 요구사항 요약
    - 기술 설계
    - 타입 정의
    - 유틸 함수 설계
    - 훅 인터페이스 설계
    - UI 설계
    - API 통합 전략
    - 파일 구조
  format: markdown

constraints:
  - RepeatInfo 타입은 이미 src/types.ts에 정의되어 있음 (활성화만 필요)
  - 백엔드 API는 이미 구현되어 있음 (server.js 참조)
  - CLAUDE.md의 모든 컨벤션 준수 필수
  - 순수 함수 원칙 준수 (utils는 side-effect 없음)
  - 반복 일정은 일정 겹침 감지에서 제외
  - 테스트 가능한 설계 (의존성 주입 고려)
  - 최소 변경 원칙 (App.tsx는 661줄, 불필요한 리팩토링 지양)

validation_criteria:
  - RepeatInfo 타입 정의 완료 및 활성화 방법 명시
  - 배치 API 타입 인터페이스 설계 완료
  - repeatUtils.ts 함수 시그니처 정의 (최소 4개 함수)
  - useEventOperations 확장 메서드 설계 (최소 3개 메서드)
  - UI 컴포넌트 구조 명확 (주석 해제 부분 식별)
  - 특수 규칙 로직 명세 완료
  - 모든 타입이 TypeScript strict mode 호환
---

# Phase 1: Feature Design - 반복 일정 기능

## 작업 개요

반복 일정 기능에 대한 상세 기술 명세서를 작성합니다. 이 명세서는 Phase 2(Test Design)와 Phase 4(Implementation)의 기반이 됩니다.

**핵심 목표**:
- 타입 시스템 설계 (이미 있는 타입 활용)
- 순수 함수 유틸리티 설계
- 훅 확장 설계
- UI 통합 설계
- 배치 API 통합 전략

## 입력 파일

### 필수 참조 파일

1. **CLAUDE.md** - 프로젝트 전체 규칙
   - 아키텍처 패턴 (Custom Hooks vs Utils 분리)
   - 코드 컨벤션 (Import 순서, 네이밍)
   - 테스트 네이밍 규칙
   - API 엔드포인트 정보

2. **Work Plan** - `.claude/agent-docs/orchestrator/logs/2025-10-30_repeat-event-plan.md`
   - 전체 작업 세분화
   - 우선순위 및 의존성
   - 리스크 평가
   - 성공 기준

3. **기존 타입 정의** - `src/types.ts`
   - RepeatInfo 인터페이스 (이미 정의됨)
   - EventForm, Event 타입 구조

4. **기존 훅 패턴** - `src/hooks/useEventOperations.ts`
   - API 통신 패턴 참고
   - 에러 처리 방식
   - 상태 관리 패턴

5. **날짜 유틸 참고** - `src/utils/dateUtils.ts`
   - 순수 함수 예시
   - 날짜 처리 패턴

6. **백엔드 API** - `server.js`
   - 배치 API 엔드포인트 확인 (`/api/events-list`, `/api/recurring-events/:repeatId`)
   - 요청/응답 구조 확인

## 산출물 요구사항

### 필수 섹션 및 내용

#### 1. 요구사항 요약
- 기능 요구사항 (사용자 관점)
- 기술 요구사항 (개발자 관점)
- 특수 규칙 명세 (31일, 윤년)
- 비기능 요구사항 (성능, 접근성)

#### 2. 기술 설계

**2.1 타입 시스템 설계**
- RepeatInfo 타입 활성화 방법
- 배치 API 요청/응답 타입 (신규 정의 필요)
- 타입 가드 함수 필요 여부

**2.2 아키텍처 결정**
- 순수 함수 (Utils) vs 훅 (Hooks) 분리 기준
- 상태 관리 전략 (기존 useEventForm 확장 vs 신규 훅)
- 에러 처리 전략

#### 3. 유틸 함수 설계 (src/utils/repeatUtils.ts)

**필수 함수**:
```typescript
// 1. 반복 일정 날짜 배열 생성 (핵심 함수)
generateRepeatDates(
  baseDate: string,
  repeatInfo: RepeatInfo
): string[]

// 2. 다음 반복 날짜 계산
getNextRepeatDate(
  currentDate: string,
  repeatType: RepeatType,
  interval: number
): string | null

// 3. 31일 매월 규칙 검증
isValid31stMonthly(date: string): boolean

// 4. 윤년 2월 29일 규칙 검증
isValidLeapYearFeb29(date: string): boolean
```

**각 함수별 명세**:
- 입력 파라미터 타입
- 반환 타입
- 예외 상황 처리
- Edge case 목록

#### 4. 훅 인터페이스 설계

**useEventOperations 확장 메서드**:
```typescript
// 1. 반복 일정 저장 (배치 API)
saveRecurringEvents(eventForm: EventForm): Promise<void>

// 2. 반복 시리즈 전체 수정
updateRecurringSeries(
  repeatId: string,
  updateData: Partial<EventForm>
): Promise<void>

// 3. 반복 시리즈 전체 삭제
deleteRecurringSeries(repeatId: string): Promise<void>
```

**각 메서드별 명세**:
- API 엔드포인트 매핑
- 요청 페이로드 구조
- 응답 처리 방법
- 에러 처리 전략
- notistack 메시지

#### 5. UI 설계

**5.1 반복 설정 폼 (App.tsx 수정)**
- 반복 체크박스 위치 (기존 주석 해제)
- RepeatType 선택 드롭다운
- 반복 간격 입력 필드
- 반복 종료일 DatePicker
- 조건부 렌더링 로직

**5.2 반복 일정 표시**
- 달력 뷰: 반복 아이콘 추가
- 일정 목록: 반복 정보 표시 형식
- 시리즈 편집/삭제 확인 다이얼로그

**5.3 접근성**
- aria-label 추가
- data-testid 추가 (테스트용)

#### 6. API 통합 전략

**6.1 배치 API 사용 시점**
- repeatType !== 'none' 일 때 `/api/events-list` 사용
- repeatType === 'none' 일 때 기존 `/api/events` 사용
- 분기 로직 위치: saveEvent 함수 내부

**6.2 repeatId 관리**
- 서버에서 자동 생성 (server.js 참조)
- 클라이언트는 repeatId를 통해 시리즈 식별

**6.3 에러 처리**
- 배치 생성 실패 시 롤백 (서버 책임)
- 클라이언트는 명확한 에러 메시지 표시

#### 7. 일정 겹침 감지 수정

**변경 사항**:
- `findOverlappingEvents` 함수 수정
- `repeat.type !== 'none'` 인 경우 겹침 체크 제외
- 기존 테스트 영향 분석

#### 8. 파일 구조

**신규 파일**:
```
src/utils/repeatUtils.ts
src/__tests__/unit/task.repeatUtils.spec.ts
src/__tests__/hooks/task.useEventOperations.spec.ts
src/__tests__/integration/task.repeat-event-integration.spec.ts
```

**수정 파일**:
```
src/types.ts (배치 API 타입 추가)
src/hooks/useEventOperations.ts (메서드 추가)
src/hooks/useEventForm.ts (필요 시 확장)
src/utils/eventOverlap.ts (겹침 제외 로직)
src/App.tsx (UI 통합)
src/__tests__/unit/easy.eventOverlap.spec.ts (테스트 추가)
```

## 제약 조건

### 1. 기존 코드 준수
- **RepeatInfo는 이미 정의됨**: `src/types.ts` 확인 후 활성화 방법만 명시
- **배치 API는 이미 구현됨**: `server.js` L76-99 참조
- **App.tsx는 661줄**: 최소 변경 원칙, 불필요한 리팩토링 지양
- **Custom Hooks 패턴**: 상태 관리 및 부수 효과만 포함
- **Utils 패턴**: 순수 함수만 포함 (외부 상태 의존 금지)

### 2. 프로젝트 컨벤션
- **Import 순서**: External → Internal (그룹 간 빈 줄)
- **함수 네이밍**: 동사+명사 (`getNextRepeatDate`)
- **Boolean 네이밍**: `is`/`has` prefix
- **타입 안전성**: TypeScript strict mode 호환
- **Single quotes, 세미콜론 필수**

### 3. 테스트 가능성
- 모든 함수는 테스트 가능하도록 설계
- 의존성 주입 고려 (필요 시)
- MSW를 위한 API 모킹 전략

### 4. 특수 규칙
- **31일 매월**: 31일이 없는 달은 건너뛰기 (예: 2월, 4월, 6월 등)
- **윤년 2월 29일**: 윤년이 아닌 해는 건너뛰기

### 5. 일정 겹침 제외
- 반복 일정(`repeat.type !== 'none'`)은 겹침 감지에서 제외
- 일반 일정 간 겹침 감지는 유지

## 검증 방법

### 자가 검증 체크리스트

명세서 작성 완료 후 다음을 확인하세요:

- [ ] RepeatInfo 타입 활성화 방법이 명확한가?
- [ ] 배치 API 타입 정의가 완료되었는가?
- [ ] repeatUtils.ts 함수 시그니처가 모두 정의되었는가? (최소 4개)
- [ ] useEventOperations 확장 메서드가 모두 정의되었는가? (최소 3개)
- [ ] UI 컴포넌트 구조가 명확한가?
- [ ] 특수 규칙(31일, 윤년) 로직이 명세되었는가?
- [ ] 일정 겹침 제외 로직이 설계되었는가?
- [ ] 모든 타입이 TypeScript strict mode 호환인가?
- [ ] 필수 섹션이 모두 포함되었는가?
- [ ] CLAUDE.md 컨벤션을 준수하는가?

### Orchestrator 검증 항목

Orchestrator가 다음을 검증합니다:

1. **파일 존재**: `.claude/agent-docs/feature-designer/logs/2025-10-30_repeat-event-spec.md`
2. **필수 섹션**: 위의 8개 섹션 모두 포함
3. **타입 정의**: 최소 2개 신규 타입 (배치 API 관련)
4. **함수 시그니처**: repeatUtils 최소 4개, 훅 메서드 최소 3개
5. **UI 구조**: 명확한 컴포넌트 계층 및 위치
6. **특수 규칙**: 31일, 윤년 로직 명세 완료

## 성공 기준

이 Phase가 성공하려면:

1. ✅ 명세서가 Phase 2(Test Design)의 입력으로 충분해야 함
2. ✅ 명세서가 Phase 4(Implementation)의 구현 가이드로 충분해야 함
3. ✅ 모든 타입, 함수 시그니처가 명확해야 함
4. ✅ 특수 규칙 로직이 구현 가능하도록 상세해야 함
5. ✅ 테스트 가능한 설계여야 함

## 다음 단계

명세서 작성 완료 후:
1. Orchestrator가 산출물 검증
2. 검증 통과 시 Phase 1 커밋 및 태그 생성
3. Phase 2 Handoff 문서 생성 (test-designer용)

---

**작성 시작 시간**: 2025-10-30T10:00:00Z
**예상 완료 시간**: 2025-10-30T11:30:00Z (1.5시간)
**우선순위**: P0 (Critical)
