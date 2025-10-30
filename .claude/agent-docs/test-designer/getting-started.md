# 🚀 Test Designer 에이전트 시작 가이드

> **목적**: Test Designer 에이전트를 처음 사용하는 사용자를 위한 빠른 시작 가이드
> **대상**: 개발자, QA 엔지니어, 테스트 아키텍트
> **소요 시간**: 약 15분

---

## 📖 문서 읽기 순서

Test Designer 에이전트를 이해하기 위해 다음 순서로 문서를 읽어주세요:

### 1️⃣ 첫 번째: 역할과 정체성 이해
📄 **[test-designer.md](../../agents/test-designer.md)** - "나는 누구인가?"

**읽는 목적**:
- Test Designer가 무엇을 하는 에이전트인지 이해
- 테스트 전략 수립의 중요성 파악
- 언제 이 에이전트를 사용해야 하는지 판단

**핵심 개념**:
- 테스트 케이스 설계 (단위, 통합, E2E)
- GWT(Given-When-Then) 패턴
- 목킹 전략 (MSW, vi.mock)
- 커버리지 목표 설정

**예상 소요 시간**: 5분

---

### 2️⃣ 두 번째: 입출력 계약 이해
📄 **[contract.md](./contract.md)** - "무엇을 주고받는가?"

**읽는 목적**:
- Phase 2 입력/출력 형식 이해
- Handoff 문서 구조 파악
- 검증 기준 확인

**핵심 개념**:
- Input Contract: Feature Spec → Test Strategy
- Output Contract: test-strategy.md 구조
- Validation Contract: GWT 패턴, 목킹 전략 검증
- Isolation Contract: 에이전트 독립성 보장

**예상 소요 시간**: 10분

---

### 3️⃣ 세 번째: 실행 방법 학습
📄 **[prompt.md](./prompt.md)** - "어떻게 하는가?"

**읽는 목적**:
- Phase 2 실행 절차 학습
- 테스트 케이스 설계 방법 이해
- 목킹 전략 수립 방법 학습

**핵심 개념**:
- Step-by-step 실행 가이드
- GWT 패턴 적용 방법
- MSW 핸들러 설계
- 커버리지 목표 설정

**예상 소요 시간**: 20분

---

## 🎯 빠른 실행 가이드

### 전제 조건

Phase 2 시작 전 다음이 완료되어야 합니다:

- ✅ Phase 1 (Feature Design) 완료
- ✅ Feature Designer가 `spec.md` 생성
- ✅ Orchestrator가 `handoff/phase2.md` 생성
- ✅ 프로젝트에 테스트 환경 구축 (Vitest, MSW)

### 폴더 구조 확인

```bash
.claude/
├── agents/
│   └── test-designer.md           # 역할 정의
├── docs/
│   └── rule-of-make-good-test.md  # 테스트 철학
└── agent-docs/
    ├── orchestrator/
    │   └── handoff/
    │       └── phase2.md            # 입력 (Orchestrator 생성)
    ├── feature-designer/
    │   └── logs/
    │       └── spec.md              # 입력 (Phase 1 산출물)
    └── test-designer/
        ├── contract.md              # 계약 명세
        ├── prompt.md                # 실행 매뉴얼
        ├── getting-started.md       # 이 문서
        ├── logs/                    # 산출물 (작업 중 생성)
        │   └── test-strategy.md     # Phase 2 산출물
        └── references/              # 참고 자료 (선택)
```

---

## 🚀 Test Designer 사용 방법

### 방법: Orchestrator의 Task Tool 호출

Test Designer는 **Orchestrator만** 호출할 수 있습니다. 사용자가 직접 호출하지 않습니다.

#### Orchestrator의 호출 프로세스

```text
Phase 1 완료 → Orchestrator 검증 → handoff/phase2.md 생성 → Test Designer 호출
```

**Orchestrator 실행 예시:**

```text
Orchestrator:
"Phase 1 산출물을 검증했습니다.
- ✅ spec.md 파일 생성됨
- ✅ 타입 정의 완료
- ✅ API 인터페이스 설계 완료

Phase 2: Test Design을 진행합니다.
Handoff 문서를 생성하고 test-designer 에이전트를 호출하겠습니다.

<uses Task tool to launch test-designer agent with:
  - subagent_type: "test-designer"
  - prompt: "Handoff 문서(.claude/agent-docs/orchestrator/handoff/phase2.md)를
             읽고 [기능명]에 대한 포괄적인 테스트 전략을 수립하세요."
>"
```

---

## 📝 첫 번째 작업 실행하기

### Step 1: Handoff 문서 확인

Test Designer가 활성화되면 가장 먼저 Handoff 문서를 읽습니다.

**Handoff 문서 경로**:
```
.claude/agent-docs/orchestrator/handoff/phase2.md
```

**예시 내용**:
```yaml
---
phase: 2
agent: test-designer
timestamp: 2025-10-30T10:30:00Z
status: ready

inputs:
  feature_spec: .claude/agent-docs/feature-designer/logs/spec.md
  context_files:
    - CLAUDE.md
    - .claude/docs/rule-of-make-good-test.md
    - src/types.ts

output_requirements:
  path: .claude/agent-docs/test-designer/logs/test-strategy.md
  required_sections:
    - 테스트 전략 개요
    - 테스트 케이스 목록
    - 목킹 계획
    - 구현 가이드

constraints:
  - GWT(Given-When-Then) 패턴 필수
  - 테스트 파일 명명: easy/medium/task
  - MSW로 API 모킹

validation_criteria:
  - 모든 핵심 동작에 테스트 케이스 존재
  - GWT 패턴 적용
  - 목킹 전략 구체적
---
```

**체크리스트**:
- [ ] feature_spec 경로 확인
- [ ] context_files 목록 확인
- [ ] output_requirements 확인
- [ ] constraints 이해
- [ ] validation_criteria 숙지

---

### Step 2: Feature Spec 분석

**파일 읽기**:
```bash
cat .claude/agent-docs/feature-designer/logs/spec.md
```

**분석 초점**:

1. **요구사항 요약**
   - 기능 개요
   - 사용자 스토리
   - 성공 기준

2. **기술 설계**
   - 컴포넌트 구조
   - 데이터 흐름
   - API 엔드포인트
   - 타입 정의

3. **테스트 가능한 동작** (중요!)
   - 핵심 동작 (GWT 형식)
   - 엣지 케이스
   - 오류 시나리오

**예시 (반복 일정 기능)**:

Feature Spec에서 다음을 추출:

```markdown
## 테스트 가능한 동작

### 핵심 동작 1: 매주 반복 일정 생성
- **Given**: 시작일, 종료일, 반복 유형 'weekly'
- **When**: generateWeeklyDates() 호출
- **Then**: 매주 같은 요일 날짜 배열 반환

### 엣지 케이스 1: 31일에 매월 반복
- **Given**: 시작일 1월 31일, 반복 유형 'monthly'
- **When**: 반복 일정 생성
- **Then**: 31일이 없는 달(2월) 제외
```

→ 이를 기반으로 테스트 케이스 설계

---

### Step 3: 테스트 전략 수립

#### 3.1 커버리지 목표 설정

**CLAUDE.md 규칙 참고**:
```yaml
핵심 사용자 경로: 100%
비즈니스 로직: 90% 이상
UI 컴포넌트: 70% 이상
유틸리티: 100%
```

**반복 일정 기능 예시**:

```markdown
## 1. 테스트 전략 개요

### 1.1 커버리지 목표

**전체 목표**: 85%

**세부 목표**:
- `src/utils/repeatUtils.ts`: 100%
  - 근거: 순수 함수, 복잡한 날짜 계산, 테스트 용이
- `src/hooks/useRepeatEvent.ts`: 90%
  - 근거: API 통신, 핵심 비즈니스 로직
- `src/components/RepeatEventForm.tsx`: 70%
  - 근거: UI 중심, 핵심 상호작용만 테스트

**전체 85%인 이유**:
- 신규 기능으로 TDD 적용 용이
- 핵심 로직(utils, hooks) 고커버리지 달성
- UI는 70%로 타협하여 평균 85%
```

#### 3.2 우선순위 설정

**P0 (필수 - 핵심 경로)**:
```markdown
#### P0 (필수)
- **반복 일정 생성 API 호출**: 핵심 기능
- **매주 반복 날짜 생성 로직**: 핵심 알고리즘
- **반복 종료 날짜 유효성 검사**: 무한 반복 방지
```

**P1 (중요 - 비즈니스 로직)**:
```markdown
#### P1 (중요)
- **매월 31일 반복 처리**: 엣지 케이스
- **윤년 2월 29일 처리**: 특수 케이스
- **반복 시리즈 삭제**: 중요하지만 복구 가능
```

**P2 (선택 - UI 세부사항)**:
```markdown
#### P2 (선택)
- **반복 유형 툴팁**: 사용자 편의
- **로딩 스피너**: UI 세부사항
- **빈 상태 메시지**: 엣지 케이스 UI
```

---

### Step 4: 테스트 케이스 작성

#### 4.1 유틸 함수 테스트 (easy.*.spec.ts)

**파일**: `src/__tests__/easy.repeatUtils.spec.ts`

**테스트 케이스 예시**:

```markdown
1. **정상 동작 - 매주 반복 날짜 생성**
   - **Given**: 시작일 2025-10-30, 종료일 2025-11-30, 반복 'weekly'
   - **When**: generateWeeklyDates(startDate, endDate)
   - **Then**: 매주 목요일 날짜 5개 반환
   - **입력 예시**:
     ```typescript
     { startDate: '2025-10-30', endDate: '2025-11-30', repeatType: 'weekly' }
     ```
   - **출력 예시**:
     ```typescript
     ['2025-10-30', '2025-11-06', '2025-11-13', '2025-11-20', '2025-11-27']
     ```

2. **경계 조건 - 매월 31일 반복**
   - **Given**: 시작일 2025-01-31, 반복 'monthly', 3회
   - **When**: generateMonthlyDates(startDate, count)
   - **Then**: 31일이 없는 달(2월) 제외
   - **입력 예시**: `{ startDate: '2025-01-31', count: 3 }`
   - **출력 예시**: `['2025-01-31', '2025-03-31', '2025-05-31']`

3. **오류 처리 - 잘못된 날짜**
   - **Given**: 잘못된 날짜 형식 'invalid-date'
   - **When**: generateWeeklyDates('invalid-date', '2025-11-30')
   - **Then**: 빈 배열 또는 에러 throw
   - **예상 오류**: `Error: Invalid date format`
```

#### 4.2 커스텀 훅 테스트 (medium.*.spec.ts)

**파일**: `src/__tests__/medium.useRepeatEvent.spec.ts`

**테스트 케이스 예시**:

```markdown
1. **초기 상태 검증**
   - **Given**: 훅 마운트
   - **When**: 초기 렌더링
   - **Then**: repeatType='none', repeatEndDate=null

2. **API 호출 성공 - 반복 일정 생성**
   - **Given**: MSW로 POST /api/events-list 성공 응답
   - **When**: createRepeatEvents(eventForm) 호출
   - **Then**:
     - isLoading: true → false
     - 성공 알림: "반복 일정이 생성되었습니다"
     - 생성된 일정 배열 반환
   - **MSW 핸들러**:
     ```typescript
     http.post('/api/events-list', () => {
       return HttpResponse.json({
         success: true,
         data: [/* 이벤트들 */]
       });
     })
     ```

3. **API 호출 실패 - 네트워크 에러**
   - **Given**: MSW로 500 에러 설정
   - **When**: createRepeatEvents(eventForm) 호출
   - **Then**:
     - 에러 알림: "반복 일정 생성에 실패했습니다"
     - 로컬 상태 변경 없음
```

#### 4.3 통합 테스트 (task.*.spec.ts)

**파일**: `src/__tests__/task.repeat-event.spec.ts`

**테스트 케이스 예시**:

```markdown
1. **전체 사용자 흐름 - 매주 반복 일정 생성**
   - **Given**: 캘린더 화면 렌더링, MSW 설정
   - **When**:
     1. "일정 추가" 폼 오픈
     2. 제목 "팀 회의" 입력
     3. 날짜 "2025-10-30" 선택
     4. 반복 유형 "매주" 선택
     5. 종료 날짜 "2025-11-30" 선택
     6. "저장" 클릭
   - **Then**:
     1. POST /api/events-list 호출
     2. 성공 알림 표시
     3. 캘린더에 5개 일정 표시
     4. 폼 초기화
   - **검증 항목**:
     - [ ] API 요청 데이터 검증
     - [ ] 로컬 상태 업데이트
     - [ ] UI 렌더링
     - [ ] 폼 초기화
```

---

### Step 5: 목킹 전략 수립

#### 5.1 MSW 핸들러 설계

**신규 핸들러**:

```typescript
// src/__mocks__/handlers.ts에 추가

// 1. 반복 일정 생성 성공
http.post('/api/events-list', async ({ request }) => {
  const events = await request.json();
  return HttpResponse.json({
    success: true,
    data: events.map((e, i) => ({ ...e, id: `repeat-${i + 1}` }))
  });
}),

// 2. 반복 일정 생성 실패
http.post('/api/events-list', () => {
  return HttpResponse.json(
    { error: '서버 오류' },
    { status: 500 }
  );
}),
```

**목 데이터 구조**:

```json
{
  "weeklyEvents": [
    {
      "id": "repeat-1",
      "title": "팀 회의",
      "date": "2025-10-30",
      "startTime": "10:00",
      "endTime": "11:00",
      "repeat": {
        "type": "weekly",
        "interval": 1,
        "endDate": "2025-11-30"
      }
    }
  ]
}
```

#### 5.2 외부 의존성 모킹

**Date 객체 모킹**:

```typescript
beforeEach(() => {
  vi.useFakeTimers();
  vi.setSystemTime(new Date('2025-10-30T09:00:00'));
});

afterEach(() => {
  vi.useRealTimers();
});
```

**notistack 모킹**:

```typescript
const mockEnqueueSnackbar = vi.fn();

vi.mock('notistack', () => ({
  useSnackbar: () => ({
    enqueueSnackbar: mockEnqueueSnackbar,
  }),
}));
```

---

### Step 6: 구현 가이드 작성

#### 6.1 코드 예시 제공

**GWT 패턴 템플릿**:

```typescript
describe('반복 일정 생성', () => {
  it('유효한 입력으로 매주 반복 일정을 생성한다', async () => {
    // Given - 테스트 데이터 준비
    const eventForm = {
      title: '팀 회의',
      date: '2025-10-30',
      // ...
    };

    // When - 동작 실행
    const { result } = renderHook(() => useRepeatEvent());
    act(() => {
      result.current.createRepeatEvents(eventForm);
    });

    // Then - 결과 검증
    await waitFor(() => {
      expect(result.current.isLoading).toBe(false);
    });
    expect(result.current.events).toHaveLength(5);
  });
});
```

#### 6.2 주의사항 명시

```markdown
### 주의사항

1. **비동기 처리 필수**
   - waitFor() 또는 findBy 쿼리 사용
   - act() 경고 방지

2. **MSW 핸들러 정리**
   ```typescript
   afterEach(() => {
     server.resetHandlers();
   });
   ```

3. **테스트 간 격리**
   - 각 테스트마다 독립적인 상태
   - beforeEach()로 초기화
```

---

### Step 7: test-strategy.md 작성

**템플릿 구조**:

```markdown
# 테스트 전략: 반복 일정 기능

## 1. 테스트 전략 개요
[커버리지 목표, 우선순위, 예상 노력]

## 2. 테스트 케이스 목록

### 2.1 유틸 함수 테스트 (easy.repeatUtils.spec.ts)
[GWT 형식 케이스들]

### 2.2 커스텀 훅 테스트 (medium.useRepeatEvent.spec.ts)
[GWT 형식 케이스들]

### 2.3 통합 테스트 (task.repeat-event.spec.ts)
[전체 흐름 시나리오]

## 3. 목킹 계획
[MSW 핸들러, 목 데이터, 외부 의존성]

## 4. 구현 가이드
[코드 예시, 주의사항, 성능 고려사항]
```

**산출물 경로**:
```
.claude/agent-docs/test-designer/logs/test-strategy.md
```

---

## 🔍 실제 예시: 반복 일정 기능

### Phase 1 산출물 (Feature Spec)

**파일**: `feature-designer/logs/spec.md`

```markdown
# 기능: 반복 일정 추가

## 1. 요구사항 요약
사용자가 매일/매주/매월/매년 반복되는 일정을 생성할 수 있다.

## 2. 기술 설계

### 2.3 API 설계
POST /api/events-list
Request: EventForm[]
Response: { success: true, data: Event[] }

### 2.4 타입 정의
```typescript
interface RepeatInfo {
  type: 'daily' | 'weekly' | 'monthly' | 'yearly';
  interval: number;
  endDate: string;
}
```

## 3. 테스트 가능한 동작

### 핵심 동작 1: 매주 반복 생성
- **Given**: 시작일, 종료일, 반복 'weekly'
- **When**: 반복 일정 생성
- **Then**: 매주 같은 요일 일정 생성
```

---

### Phase 2 산출물 (Test Strategy)

**파일**: `test-designer/logs/test-strategy.md`

```markdown
# 테스트 전략: 반복 일정 기능

## 1. 테스트 전략 개요

### 1.1 커버리지 목표
**전체 목표**: 85%

**세부 목표**:
- repeatUtils.ts: 100%
- useRepeatEvent.ts: 90%
- RepeatEventForm.tsx: 70%

### 1.2 테스트 우선순위

#### P0 (필수)
- 반복 일정 생성 API 호출
- 매주/매월 반복 날짜 생성
- 반복 종료 날짜 유효성 검사

#### P1 (중요)
- 31일 매월 반복 처리
- 윤년 처리
- 반복 시리즈 삭제

#### P2 (선택)
- 툴팁, 로딩 UI

### 1.3 예상 노력
- 총 테스트 파일: 3개
- 총 테스트 케이스: 15개
- 예상 작업 시간: 4시간

## 2. 테스트 케이스 목록

### 2.1 유틸 함수 (easy.repeatUtils.spec.ts)

1. **매주 반복 날짜 생성 - 정상**
   - **Given**: startDate='2025-10-30', endDate='2025-11-30', type='weekly'
   - **When**: generateWeeklyDates()
   - **Then**: 5개 목요일 배열
   - **입력**: `{ startDate, endDate, type }`
   - **출력**: `['2025-10-30', '2025-11-06', ...]`

[... 계속 ...]

## 3. 목킹 계획

### 3.1 MSW 핸들러

```typescript
http.post('/api/events-list', () => {
  return HttpResponse.json({
    success: true,
    data: [/* ... */]
  });
})
```

[... 계속 ...]

## 4. 구현 가이드

### 4.1 코드 예시

```typescript
describe('반복 일정 생성', () => {
  it('유효한 입력으로 매주 반복 일정을 생성한다', async () => {
    // Given
    // When
    // Then
  });
});
```

[... 계속 ...]
```

---

## ⚠️ 주의사항

### 1. GWT 패턴 엄수

모든 테스트 케이스는 GWT 형식으로 작성:

```
✅ 올바른 예:
- **Given**: 시작일 2025-10-30, 종료일 2025-11-30, 반복 'weekly'
- **When**: generateWeeklyDates(startDate, endDate)
- **Then**: 매주 목요일 5개 반환

❌ 잘못된 예:
- 매주 반복 테스트
```

### 2. 테스트 파일 명명 규칙

```yaml
easy.*.spec.ts: 순수 함수, 단순 로직
medium.*.spec.ts: 훅, API 통신
task.*.spec.ts: 신규 기능 통합 테스트

✅ 올바른 예:
- easy.repeatUtils.spec.ts
- medium.useRepeatEvent.spec.ts
- task.repeat-event.spec.ts

❌ 잘못된 예:
- repeatUtils.test.ts
- useRepeatEvent.spec.ts (접두사 없음)
```

### 3. 목 데이터 타입 일치

목 데이터는 `src/types.ts`와 일치해야 함:

```typescript
// ✅ 올바른 예
const mockEvent: Event = {
  id: '1',
  title: '테스트',
  date: '2025-10-30',
  // ... 모든 필드 포함
};

// ❌ 잘못된 예
const mockEvent = {
  title: '테스트', // id 누락
};
```

### 4. 실제 테스트 코드 작성 금지

Test Designer는 **전략만** 수립:

```
✅ Test Designer 역할:
- 테스트 케이스 설계 (GWT 형식)
- 목킹 전략 수립
- 코드 예시 제공

❌ Test Designer가 하지 않는 것:
- 실제 테스트 파일 생성 (Test Writer 역할)
- 프로덕션 코드 작성 (Code Writer 역할)
```

---

## 🆘 문제 해결

### Q: Feature Spec에 테스트 정보가 부족해요

**A**: 필요한 정보를 명시하고 Phase 1 재작업 요청:

```markdown
## ⚠️ Feature Spec 불완전

### 누락 항목
- [ ] API 에러 응답 형식 미정의
- [ ] 엣지 케이스 고려 부족

### 필요한 정보
1. POST /api/events-list 에러 응답 구조는?
2. 네트워크 실패 시 재시도 로직이 있는가?

### 조치
Orchestrator에게 Phase 1 재검토 요청
```

---

### Q: 테스트하기 어려운 설계예요

**A**: 리팩터링 제안:

```markdown
## 🤔 테스트 가능성 문제

### 문제
복잡한 로직이 컴포넌트 내부에 포함됨
→ 테스트 매우 어려움

### 제안
**대안 1: 커스텀 훅으로 분리**
- 장점: 단위 테스트 가능
- 단점: 추가 파일

**대안 2: 유틸 함수로 추출**
- 장점: 완전히 독립적 테스트
- 단점: 구조 변경

### 권장
대안 2 (유틸 함수) - 테스트 용이성 최대화
```

---

### Q: 커버리지 목표를 어떻게 설정하나요?

**A**: CLAUDE.md 규칙을 따르되, 기능 특성 고려:

```markdown
### 기본 원칙 (CLAUDE.md)
- 핵심 경로: 100%
- 비즈니스 로직: 90%
- UI: 70%
- 유틸: 100%

### 반복 일정 기능 예시
- repeatUtils.ts: 100% (순수 함수, 핵심 로직)
- useRepeatEvent.ts: 90% (API 통신, 비즈니스 로직)
- RepeatEventForm.tsx: 70% (UI, 핵심 상호작용만)

### 목표 = 평균 85%
- 근거: 신규 기능으로 TDD 적용 가능, 고품질 달성 가능
```

---

## 📚 추가 학습 자료

### 프로젝트 규칙
- [CLAUDE.md](../../../CLAUDE.md) - 프로젝트 테스트 규칙
- [rule-of-make-good-test.md](../../docs/rule-of-make-good-test.md) - 테스트 철학

### 테스트 도구 문서
- [Vitest 공식 문서](https://vitest.dev/)
- [React Testing Library](https://testing-library.com/react)
- [MSW 공식 문서](https://mswjs.io/)

### 다른 에이전트
- [feature-designer.md](../../agents/feature-designer.md) - Phase 1 설계
- [test-writer.md](../../agents/test-writer.md) - Phase 3 테스트 작성
- [orchestrator.md](../../agents/orchestrator.md) - 전체 조율

---

## 🎓 핵심 개념 복습

### 1. GWT 패턴

```
Given (준비) - 초기 상태, 전제 조건
When (실행) - 테스트할 동작
Then (검증) - 예상 결과
```

**예시**:
```markdown
- **Given**: 시작일 2025-10-30, 반복 'weekly'
- **When**: generateWeeklyDates() 호출
- **Then**: 매주 목요일 5개 반환
```

### 2. 테스트 피라미드

```
        /\
       /E2E\       ← 적음 (통합 테스트)
      /------\
     /통합 테스트\   ← 중간 (medium.*.spec.ts)
    /----------\
   /  단위 테스트  \  ← 많음 (easy.*.spec.ts)
  /--------------\
```

### 3. 목킹 최소화 원칙

```
✅ 모킹해야 하는 것:
- API 호출 (MSW)
- 시간 (vi.useFakeTimers)
- 외부 라이브러리

❌ 모킹하지 않는 것:
- 순수 함수
- 컴포넌트
- 커스텀 훅
```

---

## ✅ 시작 전 체크리스트

다음 항목을 모두 확인했다면 준비가 완료되었습니다:

- [ ] [test-designer.md](../../agents/test-designer.md) 읽기 완료
- [ ] [contract.md](./contract.md) 읽기 완료
- [ ] [prompt.md](./prompt.md) 읽기 완료
- [ ] CLAUDE.md 테스트 규칙 숙지
- [ ] GWT 패턴 이해
- [ ] MSW 기본 개념 이해
- [ ] 테스트 파일 명명 규칙 숙지
- [ ] Phase 1 (Feature Spec) 완료 확인

---

## 📊 Phase 2 산출물 예시

### test-strategy.md 구조

```markdown
# 테스트 전략: [기능명]

## 1. 테스트 전략 개요
### 1.1 커버리지 목표
### 1.2 테스트 우선순위
### 1.3 예상 노력

## 2. 테스트 케이스 목록
### 2.1 유틸 함수 테스트 (easy.*.spec.ts)
### 2.2 커스텀 훅 테스트 (medium.*.spec.ts)
### 2.3 통합 테스트 (task.*.spec.ts)

## 3. 목킹 계획
### 3.1 MSW 핸들러
### 3.2 외부 의존성 모킹
### 3.3 컴포넌트 모킹

## 4. 구현 가이드
### 4.1 테스트 작성 패턴
### 4.2 흔히 발생하는 실수
### 4.3 성능 고려사항
```

---

**다음 단계**: Orchestrator가 Phase 2 검증 후 Phase 3 (RED - Test Writing)로 진행합니다.

**문의**: 문제가 발생하면 `references/issues-log.md`에 기록하고 문서를 재확인하세요.

---

**마지막 업데이트**: 2025-10-30
**버전**: 1.0.0
**작성자**: Test Designer 개발팀
