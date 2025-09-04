# Figma Dev Mode MCP Server Guide - 통합 문서

## 📚 문서 구조
이 문서는 Figma to Code 변환 프로젝트의 모든 가이드와 학습 내용을 통합한 메인 문서입니다.

### 관련 문서들
- **MCP_UNDERSTANDING.md**: MCP 프로토콜의 본질과 작동 원리
- **FIGMA_CONVERSION_GUIDE.md**: 프로젝트 전체 가이드 및 완성된 컴포넌트
- **FIGMA_TO_ECHARTS.md**: Figma 차트 → ECharts 변환 가이드
- **FIGMA_TO_TABULATOR.md**: Figma 테이블 → Tabulator.js 변환 가이드
- **FIGMA_TABULATOR_IMPLEMENTATION.md**: Tabulator 실제 구현 사례
- **FIGMA_USE_TIP.md**: Figma 활용 팁과 개발자를 위한 사용법
- **FIGMA_PROMPT_GUIDE.md**: 효과적인 프롬프트 작성 가이드

## 🚀 빠른 시작 가이드

### 필수 요구사항
- ✅ Figma Pro/Org/Enterprise 플랜의 Dev 또는 Full seat
- ✅ Figma Desktop 앱 (웹 버전 불가)
- ✅ Claude Code 또는 지원하는 IDE

### 1단계: Figma에서 MCP 서버 활성화
1. Figma Desktop 앱 실행
2. Figma Design 파일 열기
3. 좌측 상단 Figma 메뉴 → 기본 설정
4. **"Dev Mode MCP 서버 활성화"** 선택
5. 서버 주소 확인: `http://127.0.0.1:3845/sse`

### 2단계: Claude Code에서 연결
```bash
claude mcp add --transport sse figma-dev-mode-mcp-server http://127.0.0.1:3845/sse
```

## 🎯 필수: MCP 4-Tool Set

```javascript
// 모든 작업 시작 시 반드시 4개 모두 호출!
const [metadata, code, image, tokens] = await Promise.all([
  mcp__figma-dev-mode-mcp-server__get_metadata(nodeId),    // 크기, 좌표
  mcp__figma-dev-mode-mcp-server__get_code(nodeId),        // 구조, 스타일  
  mcp__figma-dev-mode-mcp-server__get_image(nodeId),       // 시각적 확인
  mcp__figma-dev-mode-mcp-server__get_variable_defs(nodeId) // 디자인 토큰
]);
```

**효과**: 수동 측정 제거, 정확도 100%, 시간 단축 80%

## 🔌 MCP 작동 원리

```
Figma 선택 → MCP 4-Tool Set 호출 → 데이터 추출 → 코드 생성
```

| 도구 | 역할 | 반환 데이터 |
|------|------|------------|
| get_metadata | 크기/위치 | `{width, height, x, y}` |
| get_code | 구조/스타일 | HTML/CSS 코드 |
| get_image | 시각 확인 | 스크린샷 |
| get_variable_defs | 디자인 토큰 | 색상, 간격 변수 |

## 📋 주요 도구 및 사용법

### MCP 도구 실제 호출 방법
```javascript
// Claude에서 실제로 호출하는 방법
mcp__figma-dev-mode-mcp-server__get_metadata(nodeId="25:448")
mcp__figma-dev-mode-mcp-server__get_code(nodeId="25:448")
mcp__figma-dev-mode-mcp-server__get_image(nodeId="25:448")
mcp__figma-dev-mode-mcp-server__get_variable_defs(nodeId="25:448")
```

### 1. get_metadata - 크기와 위치 정보
**가장 중요한 도구** - 정확한 px 단위 크기와 좌표를 제공합니다.
```javascript
// 반환 예시
{
  "width": 1860,
  "height": 430,
  "x": 30,
  "y": 66,
  "children": [...] // 자식 요소들의 메타데이터
}
```

### 2. get_code - 코드 생성
Figma 선택 영역의 코드를 생성합니다.

**⚠️ 중요: get_code 호출 후 반드시 get_image를 호출하여 시각적 컨텍스트 확인 필수**

**사용 방법:**
- **선택 기반**: Figma에서 프레임/레이어 선택 → "현재 선택한 항목 구현해줘"
- **링크 기반**: Figma에서 링크 복사 → URL 제공하여 요청

**프레임워크 커스터마이징:**
```
"Figma 선택 항목을 Vue로 생성해줘"
"일반 HTML + CSS로 생성해줘" (이 프로젝트 기본값)
"React + Tailwind로 생성해줘" (MCP 기본값)
```

### 3. get_variable_defs - 디자인 토큰 추출
선택 항목의 변수와 스타일을 추출합니다.
- 색상, 간격, 타이포그래피 등
- 디자인 시스템 토큰 활용

### 4. get_code_connect_map - 컴포넌트 매핑
Figma 노드를 코드베이스 컴포넌트와 연결합니다.
```javascript
{
  '1:2': { 
    codeConnectSrc: 'components/Button.tsx',
    codeConnectName: 'Button'
  }
}
```

### 5. get_image - 스크린샷 생성
선택 항목의 정확한 레이아웃 스크린샷을 제공합니다.
- 환경설정에서 활성화 필요
- 토큰 사용량 증가 주의

## 💡 효과적인 사용 팁

### Figma 파일 구조화
1. **컴포넌트 사용**: 재사용되는 모든 요소를 컴포넌트로
2. **의미있는 네이밍**: `Group 5` → `CardContainer`
3. **오토레이아웃**: 반응형 의도 전달
4. **변수 활용**: 간격, 색상, 반경에 변수 사용
5. **Code Connect**: 코드베이스와 컴포넌트 연결

### 프롬프트 최적화
1. **큰 선택 분해**: 화면을 작은 컴포넌트로 나누기
2. **특정 도구 지정**: 원하는 도구 명시적 요청
3. **사용자 규칙 추가**: 팀 코딩 컨벤션 규칙 설정

### 문제 해결
- **속도 느림/오류**: 선택 범위를 작게 분해
- **잘못된 코드**: 프롬프트에 프레임워크 명시
- **토큰 대신 원시값**: `get_variable_defs` 사용 요청

## 🎯 Figma to Code 작업 공통 원칙

### 필수 작업 순서 (반드시 지켜야 함)
1. **구조 파악 우선**
   - `get_metadata`로 전체 구조 확인
   - 큰 레이아웃 블록부터 파악
   - 내부 섹션은 나중에

2. **레이아웃 먼저, 디테일은 나중에**
   - 큰 4~5개 메인 블록 먼저 구현
   - 정확한 좌표(x, y)와 크기(width, height) 반영
   - 내부 구현은 레이아웃 완성 후

3. **임의 해석 금지**
   - Figma에 없는 스타일 추가 금지
   - 배경색, 그림자 등 임의로 넣지 않기
   - 디자인에 있는 그대로만 구현

4. **디버깅 시각화**
   - hover 효과로 각 블록 경계 표시
   - node-id를 표시하여 Figma와 매칭 확인
   - 개발 완료 후 제거

5. **점진적 구현**
   - 한 번에 모든 것을 구현하려 하지 않기
   - 레이아웃 → 섹션 → 컴포넌트 → 디테일 순서

## 🔧 프로젝트 특화 설정

### 이 프로젝트에서 사용할 설정
```javascript
// 기본 프레임워크
clientFrameworks: "vanilla"
clientLanguages: "html,css,javascript"

// 디자인 토큰 우선 사용
- get_variable_defs로 토큰 먼저 추출
- CSS 변수로 변환하여 사용

// 컴포넌트 구조
- BEM 네이밍 컨벤션
- 시맨틱 HTML 태그 사용
- Flexbox/Grid 우선, absolute 최소화
```

## 📝 작업 플로우

1. **4-Tool Set 호출** (위 코드 참조)
2. **구현**: 큰 레이아웃 → 섹션 → 컴포넌트 → 디테일
3. **검증**: Figma와 픽셀 단위 비교

## 🚨 주의사항

- **Beta 상태**: 기능 변경 및 버그 가능성
- **Desktop 앱 필수**: 웹 버전에서는 작동 안 함
- **로컬 서버**: 127.0.0.1:3845에서 실행
- **토큰 관리**: get_image는 토큰 사용량 증가

## 📚 참고 링크

- [Figma 프로젝트 파일](https://www.figma.com/design/v6RuBuhzhpX4LrHSAG86sV/NewStyle_Light)
- [Figma Dev Mode 공식 문서](https://help.figma.com/hc/en-us/articles/32132100833559)
- [MCP 프로토콜 문서](https://modelcontextprotocol.io)
- [Pretendard 폰트](https://github.com/orioncactus/pretendard)
- [ECharts 공식 문서](https://echarts.apache.org)
- [Tabulator 공식 문서](https://tabulator.info)

## 🎯 현재 작업 중인 프로젝트

### Figma 파일 정보
- **메인 Figma 파일**: https://www.figma.com/design/v6RuBuhzhpX4LrHSAG86sV/NewStyle_Light
- **작업 중인 테마들**:
  - `new-dark-theme-main` (node-id: 29:2449 등)
  - `new-dark-theme-sub` (node-id: 구체적 ID 필요)
  - `new-light-theme-main` (node-id: 25:445)
  - `new-light-theme-sub` (node-id: 구체적 ID 필요)

### 구현 현황
| 테마 | main | sub |
|------|------|-----|
| dark-theme | 90% | 80% |
| light-theme | 85% | 70% |







### 핵심 학습 사항
1. **포지셔닝 전략**:
   - Absolute positioning 최소화 - "웬만하면 absolute 쓰지말자"
   - Normal flow 우선, margin으로 미세 조정
   - 부모 오프셋 반영 필수 (내부 요소 y좌표 계산)
   - Container와 overlay 관계에서만 absolute 사용

2. **Figma 정확성**:
   - Tailwind 클래스 제거, 순수 CSS 사용
   - 브라우저 렌더링 차이 수용 (폰트 높이 등)
   - Figma 수치 절대 준수 - "임의 수정은 안돼"
   - get_metadata로 정확한 px 값 확인

3. **디버깅 도구화**:
   - Hover 효과로 섹션 경계 시각화
   - Node-id 라벨링으로 Figma 매칭 용이
   - 색상별 구분으로 계층 구조 명확화
   - 개발 완료 후 제거 가능한 구조

4. **협업 패턴**:
   - 문제 지적 → 원인 분석 → 해결 제안 → 검증
   - "그렇게 쉽게 동의하면 안돼" - 깊이 있는 분석
   - 시각적 피드백 + 구체적 수치 제공
   - 점진적 개선과 즉각적 피드백

### 최근 작업 내용 (2025-09-02)
1. **Content02 Section01 완성** (node-id: 25:462):
   - BEM 네이밍 컨벤션으로 리팩토링 (section01-refactor.css)
   - 차트 영역 구현: Y축 레이블, 그리드, Area 차트, 현재 라인
   - 통계 그룹: 총 인입, 응대, 응대율 (정확한 폰트/색상 적용)
   - 필요 SVG 다운로드: area-chart, grid-x, axis-x, line-56, divider, bullet-purple

2. **Content02 Section02 완성** (node-id: 25:499):
   - BEM 네이밍 컨벤션으로 처음부터 구현
   - 승인/거절 거래 건수 테이블 (2개 그룹, 각 3행)
   - 증감률 화살표 정확한 위치 구현 (CSS Transform 이슈 해결)
   - 필요 SVG 다운로드: arrow-up-red, arrow-down-green, divider-vertical, bullet-small-blue
   - **중요한 학습**: Figma 좌표 vs CSS Transform 상호작용 이해

### 중요한 학습 사항 (2025-09-02 추가)

#### CSS Transform과 Figma 좌표의 관계
**문제**: Figma에서 초록 아래 화살표가 x=267, y=23으로 되어있었는데, 이를 그대로 적용하니 시각적으로 위치가 맞지 않음

**원인 분석**:
- `transform: rotate(180deg)` 적용 시 요소의 위치 기준점이 변경됨  
- Figma 좌표는 transform 적용 전 기준이었음
- 실제로는 빨간 위 화살표와 같은 위치(left: 7px, top: 12px)에서 회전해야 함

**해결 과정**:
1. Figma 좌표 정확히 적용 → 시각적 불일치 발견
2. 원인 분석: Transform으로 인한 좌표 변화
3. `transform-origin: center center` 명시적 설정
4. 위치 조정: y=23 → y=12, x=19px → x=7px

**핵심 교훈**:
- **Figma 우선 원칙**: 항상 Figma 좌표를 먼저 정확히 구현
- **시각적 검증**: 구현 후 디자인과 비교하여 문제 파악  
- **원인 분석**: 임의 수정 금지, 왜 안 맞는지 분석 우선
- **교정은 마지막**: Figma 정보 구현 후에만 필요시 조정

### 이전 작업 내용 (2025-01-01)
1. **new-dark-theme 픽셀 퍼펙트 수정**:
   - Content02 섹션 스타일 정확히 매칭
   - Browser Event 레이아웃 오버플로우 해결
   - CSS 삼각형 화살표 렌더링 버그 수정
   - SVG 아이콘으로 교체
   - Live Server 설정 추가

2. **new-light-theme 프로젝트 시작**:
   - Figma 구조 정확히 분석 (node-id: 25-445)
   - 4개 메인 레이아웃 블록 구현
     * header (25:864): 29, -1, 1860x75
     * content01 (25:606): 30, 66, 1860x430  
     * content02 (25:461): 29, 475, 1861x269
     * browser-event (25:448): 29, 770, 1861x303
   - 디버깅용 hover 효과 추가
   - 임의 스타일 제거, 순수 레이아웃만 구현

### 핵심 학습 사항
1. **Figma 우선 접근법**:
   - 전체 구조 먼저 파악 (get_metadata)
   - 정확한 좌표와 크기 반영
   - 임의 스타일 추가 금지

2. **디버깅 전략**:
   - hover 효과로 레이아웃 경계 확인
   - 메인 블록은 실선, 서브 섹션은 점선
   - node-id를 라벨로 표시

3. **점진적 구현**:
   - 큰 레이아웃 먼저, 세부사항은 나중에
   - 각 컴포넌트 독립적으로 개발
   - 문제 진단 후 해결

### 현재 진행 상황 (2025-09-02)
- ✅ Header 구현 완료
- ✅ Content01 구현 완료 (Section01: 거래현황, Section02: 트랜젝션 현황, Main Component: 업무시스템현황)
- ✅ Content02 Section01 완성 (콜센터 ARS 현황)
- ✅ Content02 Section02 완성 (승인 현황)
- 🟡 Content02 Section03 구현 예정 (서비스 TOP5)
- ⏳ Browser Event 구현 예정

### 다음 단계
1. **Content02 Section03 구현**
   - Figma 구조 분석 (node-id 확인 필요)
   - BEM 네이밍 컨벤션 적용
   - 서비스 TOP5 테이블 구현

### 핵심 원칙 재확인
- **픽셀 퍼펙트**: Figma 디자인과 100% 일치
- **시맨틱 HTML**: 의미있는 태그와 클래스명 사용
- **현대적 CSS**: Flexbox/Grid 우선, absolute 최소화
- **디자인 토큰**: CSS 변수로 일관성 유지
- **맥락 보존**: MCP를 통해 디자인 의도 정확히 전달

## 🎯 작업 원칙 (2025-01-01 업데이트)

### 절대 원칙
1. **Figma 100% 준수**
   - 임의 판단 금지
   - Figma에서 제공하는 모든 요소 그대로 사용
   - SVG/이미지가 있으면 반드시 다운로드하여 사용
   - CSS로 대체 가능해 보여도 Figma 리소스 우선

2. **정확한 수치 사용**
   - "조금", "약간" 같은 모호한 표현 금지
   - 구체적인 px, % 값 사용
   - Figma의 정확한 수치 확인 후 적용

3. **검증 우선**
   - 확실하지 않은 정보는 추측 금지
   - "~일 것 같습니다" 대신 실제 확인
   - 문서만 보고 판단하지 말고 실제 동작 확인

### 작업 프로세스
1. **Figma 분석**
   - `get_code`로 구조 파악
   - `get_image`로 시각적 확인
   - **`get_metadata`로 정확한 치수 확인** (중요!)
   - 필요한 모든 SVG/이미지 다운로드

#### ⚠️ Figma bg 프레임 주의사항
Figma에서 종종 나타나는 'bg' 프레임은 **디자인 도구 전용**입니다:
- **특징**: 
  - 메인 프레임보다 크게 설정 (예: 1940x1100 vs 1920x1080)
  - 음수 좌표 사용 (x: -10, y: -10)
  - hidden="true" 속성
- **퍼블리싱 규칙**: 
  - bg 프레임은 무시하고 메인 프레임의 스타일만 사용
  - 배경색/이미지는 메인 프레임에 직접 적용
  - 오버사이즈 효과나 edge bleeding은 구현하지 않음

2. **단계별 구현**
   - 레이아웃 골격 먼저
   - **컨테이너 크기는 명시적으로 설정** (자동 계산 의존 X)
   - 컴포넌트 하나씩 추가
   - 각 단계마다 커밋

3. **검증**
   - Figma 이미지와 픽셀 단위 비교
   - 색상, 크기, 위치 정확성 확인
   - **사용자가 제공한 구체적 수치 우선 적용**

### 학습된 주요 사항

1. **포지션 계산 정확성** (2025-09-02 추가)
   - 절대 좌표와 상대 좌표 변환 시 부모 오프셋 반영 필수
   - 예: main-component 내부 요소들 top 값 = Figma y좌표 - 66px (부모 y좌표)
   - Normal flow에서 margin 활용: margin-top: -8px로 정확한 y=66 위치
   - Container flexbox와 absolute overlay 혼용 시 주의

2. **Figma 치수 정확성** (2025-09-01 추가)
   - get_code의 Tailwind 클래스에만 의존하지 말 것
   - get_metadata로 각 요소의 정확한 px 값 확인 필수
   - 예: event-status 컨테이너 169px (버튼 24px × 6 + gap 5px × 5)
   - ~~자동 계산 의존 대신 명시적 크기 설정~~ → **Flexbox 레이아웃 주의사항** 참조
   - 사용자가 Figma에서 가져온 수치는 최우선 반영
   
   **Flexbox 레이아웃 주의사항**:
   - flex-wrap과 align-content 사용 시 명시적 높이가 오히려 레이아웃을 방해할 수 있음
   - 자연스러운 흐름이 더 정확한 경우가 많음
   - event-counts처럼 `justify-content: center`만으로 충분한 경우도 있음
   - Figma의 높이값은 "결과적 높이"일 수 있으니, 강제하기 전에 테스트 필요

2. **차트 구현 전략 변경**
   - ECharts → 정적 SVG/HTML로 변경
   - 퍼블리싱 정확도 우선, 나중에 동적 라이브러리 교체 가능
   - Figma SVG 직접 사용으로 100% 디자인 일치
   - 막대 차트는 개별 div 요소로 정적 구현

2. **아이콘 처리**
   - CSS 도형 대신 Figma SVG 사용
   - 타이틀 bullet: 육각형 SVG
   - 레전드: 원형 SVG
   - 모든 아이콘은 Figma에서 다운로드

3. **레이아웃 정확성**
   - box-sizing: border-box 활용
   - 패딩 포함한 전체 크기 계산
   - flexbox gap vs margin 차이 이해
   - Figma 정확한 수치 사용 (121x113px 차트 등)

4. **정적 구현의 장점**
   - 라이브러리 의존성 제거
   - 빠른 로딩 속도
   - Figma 디자인과 픽셀 단위 일치
   - 나중에 필요시 동적 교체 용이

5. **Figma HUG 속성 이해** (2025-09-02 추가)
   - HUG = 콘텐츠에 맞춰 크기 자동 조절
   - Figma HUG height ≠ 브라우저 line-height
   - 해결: line-height를 Figma HUG px 값으로 명시적 설정
   - 예: font-size 18px, line-height 1 → line-height: 13px (Figma HUG)

6. **CSS 네이밍 전략의 중요성** (2025-09-02 추가)
   - **BEM vs 중첩 선택자 비교**:
     - BEM: `.content02-section01__title` (명확, 독립적)
     - 중첩: `.content02 .section01 .title` (우선순위 복잡)
   - **dark-theme vs light-theme 구현 차이**:
     - dark-theme: BEM 스타일로 처음부터 구현 → 간단명료
     - light-theme: 중첩 선택자로 시작 → 복잡해짐 → BEM으로 리팩토링
   - **교훈**: 
     - 재사용성보다 명확성이 더 중요
     - 클래스명이 길어도 유지보수가 쉬운 것이 장기적으로 유리
     - "Simple is better than complex"

### 현재 진행 상황 (2025-09-03)
- ✅ Header 구현 완료
- ✅ Content01 완전 구현 완료
  - Section01: 서버 3D 비주얼 (17개 레이어 SVG, 역순 배치)
  - Section02: 6개 서버 카드 (노란색, 파란색, 회색 서버)
  - 배경 그라데이션 및 블렌드 모드 적용
- ✅ Content02 차트 섹션 구현 완료
  - CPU, 메모리, 디스크 사용률 3개 차트
  - Flexbox 기반 레이아웃 (absolute 최소화 원칙)
  - 차트 내부만 absolute positioning 사용
  - white-space: pre로 X축 라벨 간격 정확히 구현
  - Y축 라벨은 개별 span, X축은 한 줄 텍스트
- ⏳ Browser Event 구현 예정

### 최근 작업 내역 (2025-09-02)
1. **Content02 Section01 구현 및 리팩토링**
   - Figma 스펙 정확히 반영 (gap: 20px, padding, flex-shrink)
   - 복잡한 차트 구조 구현 (Y축, grid, area chart, current line)
   - SVG 에셋 활용 (area-chart, grid-x, axis-x, line-56, divider)
   - **중첩 선택자 → BEM 리팩토링**:
     - 287줄 → 262줄로 CSS 간소화
     - 클래스명 충돌 위험 제거
     - 디버깅 및 유지보수성 대폭 향상

2. **Content02 Section02 구현**
   - 승인 현황 테이블 (승인 거래 건수, 거절 거래 건수)
   - 3x3 그리드 레이아웃 (평균/현재/증감율)
   - 화살표 아이콘 (arrow-up-red.svg, arrow-down-green.svg)
   - BEM 네이밍으로 일관성 유지

3. **Content02 Section03 구현**
   - 서비스 TOP5현황 순위 테이블
   - 순위별 프로그레스 바 (width 다르게 설정)
   - rank 1은 어두운 색, 나머지는 파란색 톤
   - 데이터 기반 width 계산 (2362→188px, 1626→130px 등)

4. **Browser Event 구현 (진행 중)**
   - 복잡한 이벤트 모니터링 테이블 구조
   - 좌측: 이벤트 상태 패널 (ALL, CR, MA, MI, WA, NO 버튼)
   - 우측: 이벤트 테이블 (5개 severity 레벨별 데이터)
   - 9개 SVG 에셋 다운로드 및 적용
   - **문제점**: HTML 컴포넌트 파일 생성 후 삭제 (일관성 유지)
   - **남은 작업**: CSS 스타일 미세 조정 필요

5. **프로젝트 구조 개선**
   - 모든 HTML은 new-light-main.html에 통합
   - CSS만 컴포넌트별로 분리
   - 불필요한 browser-event.html 삭제로 일관성 유지

### 이전 작업 내역 (2025-09-01)
1. **Browser Event 레이아웃 디버깅**
   - 버튼 오버플로우 문제 해결 (gap 20px → 5px)
   - ALL 버튼 구조 단순화 (구분선 제거)
   - event-status 높이: 명시적 설정 후 제거 (자연스러운 흐름)
   - event-counts: justify-content: center만으로 해결
   - **핵심 교훈**: Flexbox에서는 명시적 높이보다 자연스러운 흐름이 때로 더 정확

### 이전 작업 내역 (2025-01-01)
1. **Section01 개선**
   - ECharts 제거 → Figma SVG 직접 사용
   - 차트 크기 121x113px로 정확히 조정
   - 데이터 영역 폰트 및 간격 Figma 수치 적용

2. **Section02 구현**
   - 트랜젝션 현황 (계정/승인)
   - 43개 막대 차트 정적 구현
   - 초록색(#9ecf50) 계정, 주황색(#f9b650) 승인
   - 모든 SVG 아이콘 Figma에서 다운로드

3. **Main Component 구현 실수 및 수정**
   - **실수**: get_code만 호출하고 get_image 생략 → 2D 평면 다이어그램으로 잘못 구현
   - **원인**: 이전 세션 컨텍스트 맹신, 시각적 확인 없이 텍스트 코드만으로 판단
   - **수정**: get_image로 확인 후 3D 아이소메트릭 큐브 디자인으로 재구현
   - **학습**: 세션 재개 시 재검증 필수, get_code 후 반드시 get_image 호출

### 핵심 학습 원칙 (⚠️ 필수 준수)
1. **Figma 도구 사용 시**
   - `get_code` 호출 후 **반드시** `get_image` 호출
   - 텍스트 코드만으로 디자인 판단 절대 금지
   - 불확실한 부분은 항상 시각적 확인

2. **세션 재개 시**
   - 이전 작업 상태 맹신 금지
   - "분석 완료" 상태도 재확인 필요
   - 컨텍스트 전환 시 항상 재검증

3. **구현 원칙**
   - 가정이나 추측 기반 구현 금지
   - "아마도 이럴 것이다" → Figma 확인
   - 시각적 확인 없는 구현 진행 금지

### 중요한 구현 이슈 및 해결 (2025-09-03)

#### 1. Figma 레이어 순서와 HTML 렌더링 순서의 차이
**문제**: new-dark-theme-sub의 Section01 3D 서버 비주얼 구현 시, Figma에서 가져온 레이어 순서대로 HTML에 배치했더니 3D 효과가 제대로 나타나지 않음. 가장 어두운 앞면이 뒤로 가고 뒷면이 앞으로 나오는 문제 발생.

**원인 분석**:
- Figma의 레이어 패널: 위에서 아래로 표시 (Shape 16 → Shape 15 → ... → Shape 1)
- Figma의 실제 렌더링: 아래 레이어가 먼저, 위 레이어가 나중에 그려짐
- HTML/CSS 렌더링: DOM 순서대로 렌더링 (먼저 나온 요소가 뒤에, 나중 요소가 앞에)

**해결 방법**:
```html
<!-- 잘못된 구현 (Figma 순서 그대로) -->
<div class="server3d">
  <img src="server-shape-16.svg">
  <img src="server-shape-15.svg">
  ...
  <img src="server-shape-1.svg">
</div>

<!-- 올바른 구현 (역순으로 배치) -->
<div class="server3d">
  <img src="server-shape-1.svg">  <!-- 가장 뒤 -->
  <img src="server-shape-2.svg">
  ...
  <img src="server-shape-16.svg"> <!-- 가장 앞 -->
</div>
```

**핵심 교훈**:
- Figma 레이어 리스트는 "스택 순서"를 나타냄 (위 = 앞, 아래 = 뒤)
- HTML 구현 시 반드시 **역순으로 배치**해야 동일한 시각적 결과
- `get_metadata`로 레이어 순서 확인 후 역순 적용 필수
- 3D 또는 레이어드 디자인 구현 시 특히 주의

#### 2. SVG 블렌드 모드와 색상 재현 문제
**문제**: Section02의 서버 카드에서 Figma와 웹 구현의 색상 차이 발생. Figma에서는 진한 색상인데 웹에서는 밝고 투명하게 보임.

**원인**:
- SVG 내부의 `mix-blend-mode` 스타일이 브라우저에서 제대로 적용되지 않음
- opacity와 blend mode가 복합적으로 작용하는 Figma 효과 재현 어려움
- CSS 변수(`--fill-0`) 미정의로 인한 색상 누락

**시도한 해결책들**:
1. CSS filter 적용: `brightness(0.7) contrast(1.4) saturate(1.3)`
2. 배경 그라데이션 추가
3. mix-blend-mode 강제 적용
4. opacity 조정

**미해결 이슈**: 완벽한 색상 매칭은 여전히 어려움. Figma의 복잡한 블렌딩 효과를 웹에서 100% 재현하기는 한계가 있음.

#### 3. Content02 차트 구현 - Flexbox vs Absolute Positioning (2025-09-03)
**문제**: 차트 내부 요소(Y축/X축 라벨, 그리드, 라인차트) 위치 지정 시 혼란 발생. Figma 코드는 flexbox 사용하는데 실제로는 absolute가 필요.

**해결 과정**:
1. **첫 시도**: CSS Grid 사용 (`grid-area: 1 / 1`) - 실패
2. **두 번째 시도**: 전체 absolute positioning - 과도함
3. **최종 해결**: 
   - 차트 섹션: Flexbox 레이아웃
   - 차트 내부만: Absolute positioning
   - Figma 코드의 정확한 수치 활용 (left: 55px, top: 147px 등)

**X축 라벨 간격 문제**:
- `white-space: pre` 필수 - HTML 공백 2개를 그대로 유지
- Y축: 개별 `<span>` 요소로 분리
- X축: 한 줄 텍스트에 `white-space: pre` 적용

**핵심 교훈**:
- Flexbox는 레이아웃 구조에, Absolute는 정밀한 위치 지정에 사용
- `white-space: pre`는 연속된 공백을 유지하는 핵심 속성

---

## 🎯 통합 워크플로우 가이드

### Figma to Code 변환 베스트 프랙티스

#### 1. 분석 단계 (Analysis Phase)
```javascript
// 필수 호출 순서
1. get_metadata(nodeId)    // 정확한 크기와 좌표
2. get_code(nodeId)        // 구조와 스타일
3. get_image(nodeId)       // 시각적 확인 (필수!)
4. get_variable_defs()     // 디자인 토큰
```

#### 2. 구현 우선순위
1. **Layout First**: 큰 레이아웃 블록 → 섹션 → 컴포넌트 → 디테일
2. **Position Strategy**: Flexbox/Grid > margin/padding > absolute
3. **Style Accuracy**: Figma 수치 100% 준수, 임의 해석 금지

#### 3. 검증 체크리스트
- [ ] Figma 수치와 정확히 일치하는가?
- [ ] 모든 SVG/이미지 다운로드되었는가?
- [ ] BEM 네이밍 컨벤션 준수하는가?
- [ ] 불필요한 absolute positioning 없는가?
- [ ] hover 효과와 node-id 라벨링 있는가?

### 사용자와 Claude의 명확한 역할 분담

#### 👤 사용자가 해야 할 일 (간소화됨)
```
1. Figma Desktop에서 요소 선택 (필수)
2. "이 요소 구현해줘" 또는 링크 제공
3. 결과 확인 및 피드백
```

#### 🤖 Claude가 MCP로 처리하는 일
```javascript
// 사용자가 요소를 선택하면 Claude가:
1. 선택된 요소의 node-id 감지
2. 4-Tool Set 동시 호출 (metadata, code, image, tokens)
3. Figma 데이터 기반 정확한 수치 추출
4. HTML/CSS 코드 생성
5. 필요시 수정 및 개선
```

#### ⚠️ 실제 한계
- Figma Desktop App + Dev Mode 필수
- 복잡한 컴포넌트는 단계별 작업 필요
- 사용자 피드백으로 완성도 향상

### 효과적인 프롬프트 전략

#### ✅ 좋은 프롬프트 예시 (간단명료)
```
"이 링크 구현해줘: https://www.figma.com/design/.../25:448"
"선택한 Browser Event 섹션 만들어줘"
"node-id 25:448 구현"
```

#### ❌ 피해야 할 프롬프트 (수동 작업)
```
"width를 1861px로 해줘" → Claude가 MCP로 자동 추출 가능
"padding이 20px인 것 같은데" → 추측 금지, MCP로 확인
"대충 비슷하게" → 정확한 수치는 MCP가 제공
```

#### 💡 핵심: 사용자는 "무엇을" 원하는지만 알려주면 됨
- ❌ HOW (어떻게): "width 1861px, height 303px로..."
- ✅ WHAT (무엇을): "이 섹션 구현해줘"

Claude가 MCP 도구로 모든 세부사항을 자동으로 처리합니다!

### 프로젝트별 도구 선택 가이드

| 컴포넌트 타입 | 권장 도구 | 대안 |
|------------|---------|------|
| 정적 레이아웃 | HTML/CSS | - |
| 테이블 | Tabulator.js | HTML table |
| 차트 | 정적 SVG → ECharts | Chart.js |
| 대시보드 | Grid + Flexbox | CSS Framework |

### 디버깅 전략

#### 레이아웃 문제
1. hover 효과로 경계 확인
2. 브라우저 개발자 도구로 계산된 스타일 확인
3. Figma 좌표와 CSS 위치 비교

#### 스타일 충돌
1. CSS 우선순위 확인 (!important 최후 수단)
2. 라이브러리 기본 스타일 오버라이드
3. 스코프 제한 (ID 선택자 활용)

### 협업 워크플로우

#### Figma 네이밍 컨벤션
- Components: `Component/[Type]/[Variant]`
- Colors: `color/[category]/[name]`
- Typography: `font/[size]/[weight]`

#### Git 커밋 메시지
```
feat: [컴포넌트명] 구현 완료
- Figma node-id: [ID]
- 정확한 수치 반영
- BEM 네이밍 적용
```

### 자동화 스크립트 템플릿

```javascript
// Figma to Code 자동 변환 스크립트
class FigmaConverter {
  async convert(nodeId) {
    // 1. Figma 데이터 추출
    const metadata = await mcp.get_metadata(nodeId);
    const code = await mcp.get_code(nodeId);
    const image = await mcp.get_image(nodeId);
    const tokens = await mcp.get_variable_defs(nodeId);
    
    // 2. 컴포넌트 타입 판별
    const type = this.detectComponentType(code);
    
    // 3. 적절한 변환기 선택
    switch(type) {
      case 'table': return this.convertToTabulator(metadata, tokens);
      case 'chart': return this.convertToECharts(metadata, tokens);
      default: return this.convertToHTML(metadata, code, tokens);
    }
  }
}
```

## 🚀 다음 단계 로드맵

### Phase 1: 컴포넌트 라이브러리 구축
- [ ] 재사용 가능한 컴포넌트 추출
- [ ] Storybook 통합
- [ ] 디자인 토큰 시스템 구축

### Phase 2: 자동화 파이프라인
- [ ] Figma 변경 감지 시스템
- [ ] 자동 코드 생성 스크립트
- [ ] CI/CD 통합

### Phase 3: 팀 확산
- [ ] 팀 가이드라인 문서화
- [ ] 워크샵 진행
- [ ] 피드백 시스템 구축

## 💡 핵심 원칙 재확인

> **"MCP First, Manual Last"** - MCP 도구 우선 사용. 수동 작업은 최후의 수단.

> **"4-Tool Set is Mandatory"** - get_metadata, get_code, get_image, get_variable_defs는 항상 함께.

> **"Figma First, Code Second"** - Figma가 정답이다. 코드는 Figma를 따라간다.

> **"Measure Twice, Code Once"** - 측정하고, 구현하고, 검증하라.

> **"Pixel Perfect is Not Optional"** - 픽셀 단위 정확도는 선택이 아닌 필수.

> **"Context is King"** - MCP를 통해 디자인 의도와 맥락을 보존하라.

---

## ✅ Figma to Code 작업 컨텍스트 체크리스트

### 작업 시작 전 확인
- [ ] Figma Desktop 앱 실행됨
- [ ] MCP 서버 연결 확인 (http://127.0.0.1:3845/sse)
- [ ] 작업할 Figma 파일 열림
- [ ] Dev Mode 활성화됨
- [ ] 작업할 node-id 확인됨

### 도구 호출 순서 (4-Tool Set 필수!)
1. [ ] `get_metadata` - 정확한 크기와 좌표 ⭐
2. [ ] `get_code` - 구조와 스타일 ⭐
3. [ ] `get_image` - 시각적 확인 ⭐
4. [ ] `get_variable_defs` - 디자인 토큰 ⭐

**⚠️ 중요**: 위 4개는 선택이 아닌 필수! 한 세트로 항상 함께 호출

### 구현 원칙
- [ ] 큰 레이아웃부터 시작
- [ ] Figma 수치 100% 준수
- [ ] 임의 스타일 추가 금지
- [ ] BEM 네이밍 컨벤션 사용
- [ ] Flexbox/Grid 우선, absolute 최소화

### 검증
- [ ] Figma 이미지와 픽셀 비교
- [ ] hover 효과로 경계 확인
- [ ] node-id 라벨 표시
- [ ] 브라우저 크로스 체크

이 체크리스트를 따르면 정확한 Figma to Code 변환이 가능합니다.

---

## 🤝 효과적인 협업 방식

### 핵심 원칙: "완벽한 한 방"이 아닌 "빠른 반복"

#### 사용자가 Dev Mode에서 확인할 것
1. **정확한 수치 확인**
   ```
   요소 선택 → 우측 패널에서 px 값 확인
   예: "여기 width가 1860px인데 지금 1861px로 되어있어"
   ```

2. **색상 코드 복사**
   ```
   색상 클릭 → HEX 값 복사
   예: "버튼 색상은 #502EE9야"
   ```

3. **간격 측정**
   ```
   Alt 누르고 마우스 이동으로 간격 확인
   예: "섹션 간 gap이 19px이야"
   ```

#### Claude가 도울 수 있는 부분
```
사용자: "node-id 25:448 선택했어. 이게 Browser Event 섹션이야"
Claude: get_metadata로 정확한 크기 확인하고 구현 시작

사용자: "높이가 303px인데 지금 305px로 보여"
Claude: CSS에서 즉시 수정

사용자: "이 부분 3D로 보이는데 레이어가 여러 개야"
Claude: get_image로 확인하고 레이어 순서 파악
```

#### 점진적 작업 방식
```
1단계: "일단 큰 박스 4개만 만들어줘"
2단계: "첫 번째 박스 내부 구현해보자"
3단계: "폰트 크기가 좀 작은 것 같아, 18px로 보이는데?"
4단계: "좋아, 다음 섹션으로 넘어가자"
```

#### 실시간 피드백 루프
```
Claude: [코드 구현]
사용자: "Figma랑 비교해보니 버튼 위치가 좀 다른데"
Claude: "get_metadata로 정확한 좌표 확인할게요"
사용자: "Dev Mode에서 보니 x: 847px이야"
Claude: [즉시 수정]
```

### 🎯 현실적인 작업 분담

#### 사용자의 역할 (간소화되었지만 중요)
1. **Figma 준비**
   - Figma Desktop App 실행
   - Dev Mode 활성화
   - 작업할 요소 선택
   
2. **요청 & 피드백**
   - "선택한 요소 구현해줘"
   - "색상이 좀 다른데?" → Dev Mode에서 확인
   - "간격 조정 필요" → 구체적 피드백

3. **검증**
   - 구현 결과와 Figma 비교
   - 필요시 추가 정보 제공

#### Claude의 역할 (MCP 도구로 자동화)
1. **데이터 추출** (MCP 4-Tool Set)
   - get_metadata: 정확한 크기와 좌표
   - get_code: 구조와 스타일
   - get_image: 시각적 확인
   - get_variable_defs: 디자인 토큰

2. **코드 생성**
   - 추출된 데이터 기반 정확한 구현
   - BEM 네이밍, Flexbox 우선 원칙 적용

3. **반복 개선**
   - 사용자 피드백 즉시 반영
   - 점진적 완성도 향상

#### 💡 MCP가 줄여주는 작업
- ✅ 수동 수치 측정 → MCP가 자동 추출
- ✅ 색상 코드 복사 → get_variable_defs로 자동화
- ✅ 간격 육안 측정 → get_metadata로 정확히
- ✅ 추측 작업 → 데이터 기반 정확한 구현

**Remember**: MCP로 정확도는 높이고, 작업 시간은 줄이고!