# Figma to HTML/CSS 변환 프로젝트

## 📋 프로젝트 개요
Figma 디자인을 HTML/CSS로 정확하게 변환하는 프로젝트입니다.

## 🎯 작업 원칙

### 1. 정확도 최우선
- Figma 디자인과 픽셀 단위까지 일치
- 색상, 폰트, 간격 등 모든 디테일 확인
- Figma Dev Mode의 코드 정보 활용
- **중요**: Figma 이미지와 실제 구현을 자주 비교하여 미세 조정

### 2. 레이아웃 전략
- **1순위**: Flexbox/Grid 사용
- **2순위**: 상대적 위치 (margin, padding)
- **3순위**: absolute는 꼭 필요한 경우만 (배경 이미지 등)

### 3. 디테일 체크포인트
- **컨테이너 padding**: 내부 요소와의 여백 확인 (특히 active 상태)
- **버튼 크기**: height 고정값 사용으로 일관성 유지
- **폰트 크기/굵기**: Figma 스펙 정확히 반영
- **border-radius**: 곡률 정확히 매칭

### 4. 에셋 관리
- 모든 SVG/이미지는 Figma에서 다운로드
- 로컬 assets 폴더에 저장
- 의미있는 파일명 사용

## 🛠️ 작업 프로세스

### 1단계: Figma 분석
```javascript
// Figma 이미지 확인
mcp__figma-dev-mode-mcp-server__get_image(nodeId)

// 스타일 정보 추출
mcp__figma-dev-mode-mcp-server__get_code(nodeId)
```

### 2단계: 에셋 다운로드
```bash
# SVG 에셋 로컬 다운로드 예시
curl -o figma/assets/icon.svg "http://localhost:3845/assets/[hash].svg"
```

### 3단계: HTML 구조 작성
- 시맨틱 태그 사용 (header, main, section, article)
- BEM 또는 의미있는 클래스명 사용
- data 속성으로 Figma node-id 유지

### 4단계: CSS 스타일링
- CSS 변수 활용
- 반응형 고려
- 애니메이션 효과 추가

## 📁 프로젝트 구조
```
figma/
├── assets/
│   ├── bullet.svg
│   ├── browser-controls.svg
│   ├── radio-btn.svg
│   ├── close-icon.svg
│   └── ...
├── mca-component.html
├── mca-component.css
├── business-system.html
├── business-system.css
├── event-dashboard.html
├── event-dashboard.css
└── event-dashboard.js
```

## 🎨 완성된 컴포넌트

### 1. MCA Component
- **파일**: mca-component.html/css
- **특징**: 
  - 단순한 아이콘과 텍스트 컴포넌트
  - Flexbox 중심 레이아웃
  - 14px 폰트 크기로 조정

### 2. Business System (업무시스템 현황)
- **파일**: business-system.html/css/js
- **특징**:
  - 절대 위치 기반 다이어그램
  - 시스템 아이콘 클릭 이벤트
  - 호버 효과 및 애니메이션
  - 9개 시스템 컴포넌트 (콜센터, 은행, MCA, EAI 등)

### 3. Event Dashboard (이벤트현황)
- **파일**: event-dashboard.html/css/js
- **특징**:
  - 브라우저 스타일 헤더
  - Grid 레이아웃으로 컨트롤 배치
  - 이벤트 테이블 (Critical, Warning, Minor, Major, Normal)
  - 상태 필터링 기능
  - Auto 새로고침 토글

### 4. Header Component
- **파일**: header.html/css/js
- **특징**:
  - TENOBIT 로고 좌측 배치
  - 중앙 GNB 메뉴 (4개 항목)
  - 활성 메뉴 형광색(#d9f460) 강조
  - 실시간 시계 (YYMMDD HH:MM:SS)
  - 로그아웃 버튼
  - 키보드 단축키 지원 (Alt+1~4, Ctrl+L)

## 💡 핵심 학습 포인트

### Grid 레이아웃 활용
```css
/* 모든 요소를 같은 셀에 겹치기 */
.controls-container {
    display: grid;
}

.browser-bg,
.btn-auto,
.btn-close {
    grid-area: 1 / 1;
}

/* margin으로 위치 조정 */
.btn-auto {
    margin-left: 47px;
}
```

### Flexbox vs Grid vs Absolute
- **Flexbox**: 일반적인 레이아웃, 정렬
- **Grid**: 요소 겹치기, 복잡한 레이아웃
- **Absolute**: 배경 이미지, 특수한 경우만

### 🎨 Figma 느낌 살리기 팁
1. **미세 조정의 중요성**
   - padding 1-2px 차이도 전체 느낌에 영향
   - 컨테이너와 내부 요소 간 여백 확인 필수
   
2. **컴팩트한 디자인**
   - Figma 디자인은 대체로 타이트하고 컴팩트
   - 과도한 padding/margin 지양
   - height 고정값으로 일관성 유지

3. **시각적 비교 습관화**
   - 작업 중 Figma 이미지와 자주 비교
   - 브라우저 개발자 도구로 실시간 조정
   - 스크린샷 찍어서 1:1 비교

4. **색상 정확도**
   - Figma의 정확한 HEX 값 사용
   - opacity/rgba 값도 정확히 반영
   - 그라데이션, 그림자 효과 확인

### 이벤트 뱃지 스타일링
```css
/* 이중 레이어 효과 */
.event-badge.critical {
    background-color: rgba(235, 197, 206, 0.6);
}

.event-badge.critical::before {
    content: '';
    position: absolute;
    inset: 4px 6px;
    background-color: rgba(243, 47, 80, 0.6);
    border: 1px solid #f43050;
    border-radius: 15px;
}
```

## 🔧 개선 사항

### 완료된 개선
- ✅ body 배경색 수정 (#122023 → #f5f5f5)
- ✅ 헤더에 "이벤트현황" 타이틀 포함
- ✅ Auto/Close 버튼 중앙 정렬
- ✅ absolute 대신 Grid 레이아웃 사용
- ✅ 로컬 에셋 관리
- ✅ Header GNB padding 조정 (4px로 컴팩트하게)
- ✅ 메뉴 버튼 높이 고정 (28px)

### 향후 개선 가능 사항
- 반응형 디자인 강화
- 다크모드 지원
- 컴포넌트 재사용성 향상
- TypeScript 타입 정의
- 접근성 개선 (ARIA labels)

## 🚀 최적화 로드맵

### 1. 디자인 토큰 시스템 구축
```css
/* 현재: 하드코딩된 값 */
background-color: #122023;
padding: 4px 64px;

/* 목표: CSS 변수로 토큰화 */
:root {
  --color-primary: #d9f460;
  --color-bg-dark: #122023;
  --color-text-default: #323232;
  --color-text-muted: #cccccc;
  
  --spacing-xs: 4px;
  --spacing-sm: 8px;
  --spacing-md: 16px;
  --spacing-lg: 32px;
  --spacing-xl: 64px;
  
  --radius-sm: 15px;
  --radius-md: 18px;
  
  --font-size-sm: 14px;
  --font-size-md: 16px;
  --font-size-lg: 20px;
}
```

### 2. 컴포넌트 기반 아키텍처
```javascript
// 재사용 가능한 컴포넌트 클래스
class FigmaComponent {
  constructor(nodeId, options) {
    this.nodeId = nodeId;
    this.style = options.style;
    this.state = options.state;
  }
  
  async sync() {
    const code = await mcp.get_code(this.nodeId);
    const vars = await mcp.get_variable_defs(this.nodeId);
    this.update(code, vars);
  }
}

// 사용 예시
const button = new FigmaComponent('197:584', {
  style: 'filled',
  state: 'active'
});
```

### 3. 자동화 워크플로우
```javascript
// scripts/sync-figma.js
async function syncFigmaDesign() {
  // 1. Figma에서 변경사항 감지
  const changes = await detectFigmaChanges();
  
  // 2. 변경된 컴포넌트 코드 생성
  for (const nodeId of changes) {
    const code = await generateCode(nodeId);
    const assets = await downloadAssets(nodeId);
  }
  
  // 3. 디자인 토큰 업데이트
  await updateDesignTokens();
  
  // 4. 컴포넌트 라이브러리 빌드
  await buildComponents();
}
```

### 4. 빌드 프로세스 통합
```json
// package.json
{
  "scripts": {
    "figma:sync": "node scripts/sync-figma.js",
    "figma:tokens": "node scripts/extract-tokens.js",
    "figma:assets": "node scripts/download-assets.js",
    "build:css": "postcss src/styles --dir dist",
    "build:components": "webpack --config webpack.config.js",
    "dev": "npm run figma:sync && npm run build:css"
  }
}
```

### 5. 컴포넌트 라이브러리 구조
```
figma-design-system/
├── tokens/
│   ├── colors.json
│   ├── typography.json
│   └── spacing.json
├── components/
│   ├── Button/
│   │   ├── Button.html
│   │   ├── Button.css
│   │   ├── Button.js
│   │   └── Button.stories.js
│   ├── Card/
│   └── Table/
├── assets/
│   └── icons/
└── scripts/
    ├── sync-figma.js
    ├── extract-tokens.js
    └── generate-component.js
```

### 6. Code Connect 활용 전략
```javascript
// figma.code-connect.js
import { Button } from './components/Button';

figma.connect(Button, 'node-id-197:584', {
  props: {
    variant: figma.enum('Style', {
      filled: 'filled',
      outline: 'outline'
    }),
    isActive: figma.boolean('Active')
  },
  example: ({ variant, isActive }) => (
    <Button variant={variant} active={isActive}>
      종합현황
    </Button>
  )
});
```

### 7. 팀 협업 프로세스 개선
- **Figma 네이밍 컨벤션**
  - Components: `Component/[Type]/[Variant]`
  - Colors: `color/[category]/[name]`
  - Typography: `font/[size]/[weight]`

- **Git 브랜치 전략**
  - `figma/[component-name]` - 컴포넌트 개발
  - `tokens/[update-type]` - 토큰 업데이트

- **자동 PR 생성**
  ```bash
  # Figma 변경 감지 시 자동 PR
  npm run figma:sync && npm run create-pr
  ```

## 📚 참고 자료

### Figma 관련
- **프로젝트 Figma 파일**: https://www.figma.com/design/v6RuBuhzhpX4LrHSAG86sV/NewStyle_Light
- **Figma Dev Mode MCP Server 공식 가이드**: https://help.figma.com/hc/en-us/articles/32132100833559-Guide-to-the-Dev-Mode-MCP-Server
  - 현재 오픈 베타 상태
  - Professional, Organization, Enterprise 플랜의 Dev/Full seat 필요
  - Figma Desktop 앱 필수

### 폰트
- **Pretendard 폰트**: https://github.com/orioncactus/pretendard

### MCP Server 주요 기능
- `get_code`: Figma 선택 영역의 코드 생성
- `get_variable_defs`: 디자인 토큰 추출
- `get_code_connect_map`: Figma 노드와 코드 컴포넌트 매핑
- `get_image`: 선택한 노드의 이미지 렌더링

## 🚀 실행 방법

### 환경 설정
1. **Figma Desktop 앱 설치 및 로그인**
2. **Claude Code에서 MCP 서버 연결**:
   ```bash
   # 명령어 직접 실행 (권장)
   claude mcp add --transport sse figma-dev-mode-mcp-server http://127.0.0.1:3845/sse
   
   # 또는 대화형 모드
   /mcp add
   → Figma 관련 서버 선택
   ```

### HTML 파일 실행
1. 브라우저에서 HTML 파일 직접 열기
2. 또는 Live Server 사용
```bash
# VS Code Live Server 또는
python -m http.server 8000
```

## 📝 메모
- Figma의 Auto Layout → CSS Flexbox
- Figma의 Grid → CSS Grid
- Figma의 Effects → CSS box-shadow, filter
- 컨트롤 영역은 Grid로 구현 (grid-area: 1 / 1로 겹치기)
- 정확한 위치는 margin-left 값으로 조정