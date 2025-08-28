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

### 현재 상황 (2025-08-28)
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

### 최근 작업 내용
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

### 프로젝트 구조
```
Figma_Conversion/
├── conversion/              # 변환 결과물
│   └── tabulator/          # Tabulator 구현물
│       └── event-monitoring-table.html
├── 문서/
│   ├── FIGMA_CONVERSION_GUIDE.md
│   ├── FIGMA_TO_ECHARTS.md
│   ├── FIGMA_TO_TABULATOR.md
│   ├── FIGMA_TABULATOR_IMPLEMENTATION.md
│   ├── FIGMA_USE_TIP.md
│   ├── MCP_UNDERSTANDING.md
│   └── CLAUDE.md (현재 파일)
```

### 핵심 학습 사항
1. **라이브러리 스타일 오버라이드**:
   - `!important` 사용은 필요악
   - 라이브러리 기본 스타일 분석 능력 중요

2. **디버깅 접근법**:
   - 문제 진단 먼저, 해결은 나중에
   - 최소한의 변경으로 문제 해결
   - 하나 고치면 다른 것이 깨지는 상황 대처

3. **효과적인 협업 패턴**:
   - 시각적 피드백(스크린샷) + 텍스트 설명
   - 점진적 개선 접근
   - 명확한 경계 설정 (외부 라이브러리, CSS 규칙)

### 다음 단계
1. **다른 Figma 컴포넌트 변환 시도**
2. **변환 자동화 스크립트 개발**
3. **컴포넌트 라이브러리 구축**
4. **반응형 디자인 적용**

### 핵심 원칙 재확인
- **픽셀 퍼펙트**: Figma 디자인과 100% 일치
- **시맨틱 HTML**: 의미있는 태그와 클래스명 사용
- **현대적 CSS**: Flexbox/Grid 우선, absolute 최소화
- **디자인 토큰**: CSS 변수로 일관성 유지
- **맥락 보존**: MCP를 통해 디자인 의도 정확히 전달