# Figma Dev Mode MCP Server Guide

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

## 🔌 MCP 도구들의 작동 방식

### MCP Server가 Figma 데이터를 가져오는 원리
MCP 서버의 도구들(`get_code`, `get_variable_defs`, `get_code_connect_map`, `get_image`)은 Figma 링크나 선택된 요소로부터 데이터를 추출하는 **브릿지 역할**을 합니다.

**작동 플로우:**
```
1. 사용자: "이 Figma URL의 버튼 구현해줘" 또는 Figma에서 요소 선택
2. Claude: URL에서 node-id 추출 (예: "1:2") 또는 선택된 요소 감지
3. MCP 도구 호출: mcp__figma-dev-mode-mcp-server__get_code(nodeId="1:2")
4. Figma Desktop → MCP Server: 해당 노드의 디자인 데이터 전송
5. MCP Server → Claude: 표준화된 JSON 포맷으로 변환된 데이터 반환
6. Claude: 받은 데이터를 HTML/CSS/JS로 변환
```

**각 도구가 가져오는 데이터:**
- **get_code**: 디자인 구조와 스타일을 코드 형태로
- **get_variable_defs**: 디자인 시스템의 변수들(색상, 간격 등)
- **get_code_connect_map**: Figma 컴포넌트와 실제 코드 컴포넌트의 연결 정보
- **get_image**: 선택한 디자인의 시각적 스크린샷

이 도구들은 Figma의 **실시간 디자인 정보**를 가져와서 코드로 변환할 수 있게 해주는 핵심 기능입니다.

## 📋 주요 도구 및 사용법

### 1. get_code - 코드 생성
Figma 선택 영역의 코드를 생성합니다.

**⚠️ 중요: get_code 호출 후 반드시 get_image를 호출하여 시각적 컨텍스트 확인 필수**

**사용 방법:**
- **선택 기반**: Figma에서 프레임/레이어 선택 → "현재 선택한 항목 구현해줘"
- **링크 기반**: Figma에서 링크 복사 → URL 제공하여 요청

**프레임워크 커스터마이징:**
```
"Figma 선택 항목을 Vue로 생성해줘"
"일반 HTML + CSS로 생성해줘"
"React + Tailwind로 생성해줘" (기본값)
```

### 2. get_variable_defs - 디자인 토큰 추출
선택 항목의 변수와 스타일을 추출합니다.
- 색상, 간격, 타이포그래피 등
- 디자인 시스템 토큰 활용

### 3. get_code_connect_map - 컴포넌트 매핑
Figma 노드를 코드베이스 컴포넌트와 연결합니다.
```javascript
{
  '1:2': { 
    codeConnectSrc: 'components/Button.tsx',
    codeConnectName: 'Button'
  }
}
```

### 4. get_image - 스크린샷 생성
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

1. **Figma에서 컴포넌트 선택**
2. **이미지 확인**: `get_image`로 디자인 확인
3. **토큰 추출**: `get_variable_defs`로 디자인 토큰 확인
4. **코드 생성**: `get_code`로 기본 구조 생성
5. **커스터마이징**: HTML/CSS로 변환 및 최적화
6. **에셋 다운로드**: SVG/이미지 로컬 저장
7. **검증**: Figma 디자인과 픽셀 단위 비교

## 🚨 주의사항

- **Beta 상태**: 기능 변경 및 버그 가능성
- **Desktop 앱 필수**: 웹 버전에서는 작동 안 함
- **로컬 서버**: 127.0.0.1:3845에서 실행
- **토큰 관리**: get_image는 토큰 사용량 증가

## 📚 참고 링크

- [Figma 프로젝트 파일](https://www.figma.com/design/v6RuBuhzhpX4LrHSAG86sV/NewStyle_Light)
- [Figma Dev Mode 공식 문서](https://help.figma.com/hc/en-us/articles/32132100833559)
- [MCP 프로토콜 문서](https://modelcontextprotocol.io)

## 📌 프로젝트 진행 컨텍스트

### 현재 상황 (2025-08-28 작업 완료, 내일 계속)
1. **문서 정리 완료**:
   - `FIGMA_CONVERSION_GUIDE.md`: 프로젝트 전체 가이드 및 완성된 컴포넌트 정리
   - `CLAUDE.md`: Figma MCP Server 설정 및 사용법 가이드
   - `MCP_UNDERSTANDING.md`: MCP 프로토콜의 본질과 작동 원리 이해
   - `FIGMA_TO_ECHARTS.md`: Figma 차트 디자인을 ECharts로 변환하는 가이드
   - `FIGMA_TO_TABULATOR.md`: Figma 테이블 디자인을 Tabulator.js로 변환하는 가이드
   - `FIGMA_TABULATOR_IMPLEMENTATION.md`: Tabulator 실제 구현 사례 및 학습 내용
   - `FIGMA_USE_TIP.md`: Figma 활용 팁과 개발자를 위한 사용법

2. **MCP 이해도 확립**:
   - MCP는 서로 다른 앱 간의 **맥락(Context)을 전달하는 표준 프로토콜**
   - Figma의 디자인 데이터를 표준화된 JSON으로 변환하여 Claude가 이해하고 코드 생성
   - 단순 데이터 전송이 아닌 **의미와 의도를 보존**하는 통신

3. **작업 준비 완료**:
   - Figma MCP Server 연결 방법 숙지
   - HTML/CSS 변환 워크플로우 확립
   - 디자인 토큰 활용 전략 수립
   - **ECharts 변환 기능 확인**: Figma 차트 디자인을 ECharts 옵션으로 성공적으로 변환

### 최근 작업 내용 (2025-09-02)
1. **Content01 완전 구현 완료**:
   - **Section01 (거래현황)** (node-id: 25:608):
     - Flexbox 기반 legend-list (column-gap: 36px, row-gap: 26px)
     - 브라우저 폰트 렌더링 차이 수용 (88px Figma vs 99.981px 실제)
     - 정적 SVG 파이차트 구현 (121x113px)
     - hover 효과 및 node-id 라벨링 추가
   
   - **Section02 (트랜젝션 현황)** (node-id: 25:641):
     - 계정/승인 각 43개 막대 차트 정적 구현
     - 초록색(#9ECF50) 계정, 주황색(#F9B650) 승인
     - SVG 라인 그래프 오버레이
     - 모든 아이콘 Figma SVG 직접 사용
   
   - **Main Component (업무시스템현황)** (node-id: 25:843):
     - 3D 아이소메트릭 비즈니스 시스템 다이어그램
     - 9개 컴포넌트 정확한 위치 구현 (상대 좌표 계산)
     - 콜센터, 카드발급, 인터넷포탈, 계정계, 승인계, 정보계, 은행, MCA, EAI
     - 배경 네트워크 다이어그램 포함
     - **위치 버그 수정**: 내부 요소들 y좌표에서 66px 빼기 (부모 오프셋 반영)

2. **레이아웃 아키텍처 개선**:
   - **Absolute 최소화 원칙 확립**: "웬만하면 absolute 쓰지말자 나중에 감당이 안돼"
   - Normal flow 기반으로 전환 (margin 활용)
   - Container는 absolute 유지 (형제 관계 main-component와 오버랩)
   - 유지보수성과 확장성 향상

3. **디버깅 도구 추가**:
   - 모든 섹션에 hover 효과 (색상별 구분)
   - Node-id 라벨 자동 표시
   - Section01: 보라색 (#502EE9)
   - Section02: 주황색 (#F9B650)
   - Main Component: 보라색 (#800080)

### 이전 작업 내용 (2025-09-01 오후)
1. **Figma bg 프레임과 배경색 이슈 해결**:
   - **문제**: Figma의 bg 프레임(25:446)과 new-light-main(25:445)의 관계 혼동
   - **해결**: bg는 Figma 디자인 도구용 컨테이너로, 퍼블리싱 시 무시
   - **배경색**: new-light-main에 #E8EDFF 직접 적용
   - **핵심 교훈**: Figma의 hidden="true" 속성이나 오버사이즈 프레임은 디자인 도구용이며, 실제 구현에서는 제외

2. **Header 컴포넌트 구현 완료** (node-id: 25:864):
   - **구조**: new-dark-theme과 동일한 방식 적용
     - HTML 마크업은 new-light-main.html에 직접 작성
     - CSS는 components/header/header.css로 분리
     - SVG 에셋은 assets/icons/ 폴더에 저장
   - **디자인 토큰**:
     - Primary: #502EE9 (활성 메뉴)
     - Grayscale1: #373D56 (GNB 배경)
     - Grayscale4: #A6B3DE (비활성 메뉴 텍스트)
   - **구현 방식**: 
     - Figma inset 값을 percentage로 정확히 변환
     - JavaScript 없이 정적 HTML/CSS만 사용
   - **학습 포인트**: 
     - 컨텍스트 유지의 중요성
     - 기존 프로젝트 구조 준수 필요성

### 이전 작업 내용 (2025-09-01 오전)
1. **헤더 컴포넌트 구현 완료** (node-id: 29:2449):
   - **구조**: top-bg(배경), bi(로고), gnb(메뉴), top-btn(시간/로그아웃)
   - **위치**: left: 29px, top: -1px, width: 1860px, height: 75px
   - **특징**: 
     - SVG 배경 이미지 사용 (header-bg.svg)
     - Flexbox 기반 GNB 메뉴 레이아웃
     - 실시간 시계 표시 영역
     - 로그아웃 버튼 호버 효과
   - **에셋**: 5개 SVG 파일 (header-bg, renobit-logo, menu-icons, logout-icon)
   - **CSS 전략**: Figma inset 값을 percentage로 직접 변환하여 정확한 위치 구현
   - **통합 방식**: object 태그 대신 직접 HTML 포함 (body margin 이슈 해결)

2. **Content01 레이아웃 구조 분석** (node-id: 29:2191):
   - **구조 발견**: container와 main-component는 형제 관계 (부모-자식 아님)
   - **container**: 거래현황 + 계정 섹션 (1860 x 374px)
   - **main-component**: 업무시스템현황 오버레이 (727.46 x 429.76px)
   - **위치 전략**: 절대 위치 지정으로 Figma 좌표 정확히 재현
   - **시각적 구분**: 색상별 경계선으로 디자이너 검증 용이

### 이전 작업 내용 (2025-08-28)
1. **Figma to ECharts 변환 실습** (node-id: 200-513):
   - "계정" 차트 컴포넌트를 ECharts 옵션으로 변환
   - 44개 막대 그래프 데이터 매핑
   - 색상, 스타일, 레이아웃 정확히 반영
   - 범례와 데이터 값(금일: 7,927,921 / 전일: 7,320,915) 포함

2. **Figma to Tabulator 구현 완료** (node-id: 203-1595):
   - 이벤트 모니터링 테이블 90% 이상 재현
   - 복잡한 이벤트 뱃지 이중 레이어 구조 완벽 구현
   - 5가지 severity 상태별 색상 정확히 매칭
   - CSS 우선순위 충돌 해결 (라이브러리 스타일 오버라이드)
   - fit-content 컨테이너 이슈 해결 (flex → inline-block)
   - 파일 위치: `conversion/tabulator/event-monitoring-table.html`

3. **헤더 컴포넌트 구현** (node-id: 197-599):
   - TENOBIT 로고, GNB 메뉴, 실시간 시계, 로그아웃 버튼
   - 메뉴: 종합현황, 인프라현황, 어플리케이션관리, 인프라운영관리
   - 키보드 단축키 지원 (Alt+1~4, Ctrl+L)
   - Pretendard 폰트 적용
   - 파일 위치: `conversion/components/header.html`

4. **대시보드 레이아웃 구현** (node-id: 214-2286):
   - 전체 대시보드 배경 및 레이아웃 구조 (1920x1080)
   - 컴포넌트 배치 위치 정의 (플레이스홀더)
   - iframe을 통한 기존 컴포넌트 통합
   - 파일 위치: `conversion/components/dashboard-layout.html`

### 프로젝트 구조
```
Figma_Conversion/
├── conversion/                          # 변환 결과물
│   ├── index.html                      # 프로젝트 인덱스 페이지
│   ├── components/                     # 컴포넌트들
│   │   ├── header.html                 # 헤더 컴포넌트
│   │   ├── dashboard-layout.html       # 대시보드 전체 레이아웃
│   │   └── assets/                     # 에셋 파일들
│   │       ├── dashboard-bg.png        # 대시보드 배경
│   │       ├── header-bg.svg           # 헤더 배경
│   │       ├── tenobit-logo.svg        # 로고
│   │       ├── logout-icon.svg         # 로그아웃 아이콘
│   │       └── menu-icon.svg           # 메뉴 아이콘
│   └── tabulator/                      # Tabulator 구현물
│       └── event-monitoring-table.html # 이벤트 모니터링 테이블
├── 문서/
│   ├── FIGMA_CONVERSION_GUIDE.md
│   ├── FIGMA_TO_ECHARTS.md
│   ├── FIGMA_TO_TABULATOR.md
│   ├── FIGMA_TABULATOR_IMPLEMENTATION.md
│   ├── FIGMA_USE_TIP.md
│   ├── MCP_UNDERSTANDING.md
│   └── CLAUDE.md (현재 파일)
└── .claude/
    └── settings.local.json              # Claude Code MCP 설정
```

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

### 최근 작업 내용 (2025-01-01)
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

### 다음 단계
1. **new-light-theme 계속 진행**
   - 각 메인 블록 내부 구현
   - Figma 디자인 정확히 따르기
   - 컴포넌트별 CSS 분리

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

### 현재 진행 상황 (2025-09-02)
- ✅ Header 구현 완료
- ✅ Section01 (거래현황) - 정적 SVG 파이차트
- ✅ Section02 (트랜젝션 현황) - 정적 막대 차트
- ✅ Main Component (업무시스템현황) - 3D 아이소메트릭 구현
- ✅ Content01 완전 구현 완료
- ⏸️ Content02 컴포넌트들 (다음 작업 예정)
- ⏸️ Browser Event (다음 작업 예정)

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