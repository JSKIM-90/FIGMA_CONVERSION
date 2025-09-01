# RENOBIT ver.3.0 Dark Theme Dashboard

## 📁 프로젝트 구조

```
new-dark-theme/
├── new-dark-main.html         # 메인 대시보드 HTML
├── components/                 # 컴포넌트별 스타일 및 HTML
│   ├── browser-event/         # 이벤트현황 컴포넌트
│   │   ├── browser-event.css
│   │   ├── event-left.css
│   │   └── event-right.css
│   ├── content01/             # 상단 섹션 컴포넌트
│   │   ├── content01.css
│   │   ├── section01.css     # 거래현황
│   │   └── section02.css     # 계정
│   ├── content02/             # 중간 섹션 컴포넌트
│   │   ├── content02.css
│   │   ├── section01.css     # 콜센터 ARS 모니터링
│   │   ├── section02.css     # 승인 현황
│   │   └── section03.css     # 서비스 TOP5현황
│   ├── main-component/        # 업무시스템현황
│   │   └── main-component.css
│   └── header/                # 헤더 컴포넌트
│       ├── header.css
│       └── header.html
├── assets/                     # 에셋 파일
│   ├── browser-event/         # 이벤트 아이콘
│   ├── charts/                # 차트 SVG
│   ├── content02/             # Content02 관련 에셋
│   ├── fonts/                 # 폰트 파일
│   ├── icons/                 # 아이콘 파일
│   │   └── legend/           # 범례 아이콘
│   └── images/                # 이미지 파일
│       └── bg.png            # 배경 이미지
└── styles/                     # 전역 스타일
    ├── main.css              # 메인 레이아웃
    ├── dark-theme.css        # 다크 테마 변수
    └── layout.css            # 레이아웃 스타일
```

## 🎨 구현 현황

### ✅ 완료된 컴포넌트

| 컴포넌트 | Figma 노드 | 설명 | 상태 |
|----------|------------|------|------|
| Header | 29:2449 | 상단 헤더 (로고, 메뉴, 시계) | ✅ 완료 |
| Content01 Section01 | 29:2193 | 거래현황 (도넛차트) | ✅ 완료 |
| Main Component | 29:2428 | 업무시스템현황 (3D 다이어그램) | ✅ 완료 |
| Content01 Section02 | 29:2226 | 계정 (막대차트) | ✅ 완료 |
| Content02 Section01 | 29:2047 | 콜센터 ARS 모니터링 | ✅ 완료 |
| Content02 Section02 | 29:2084 | 승인 현황 (테이블) | ✅ 완료 |
| Content02 Section03 | 29:2153 | 서비스 TOP5현황 | ✅ 완료 |
| Browser Event | 29:2033 | 이벤트현황 (모니터링 테이블) | ✅ 완료 |

### 🔧 최근 수정사항

1. **폴더 구조 정리**
   - 불필요한 폴더 제거 (charts/, data/, scripts/, sections/, tables/)
   - 컴포넌트 중심의 간결한 구조로 재편성

2. **스타일 픽셀 퍼펙트 수정**
   - Content02 섹션 배경색 및 그라데이션
   - SVG 아이콘 교체
   - CSS 삼각형 렌더링 버그 수정

3. **Browser Event 레이아웃 최적화**
   - Flexbox 오버플로우 문제 해결
   - 자연스러운 높이 조정

## 🚀 로컬 실행 방법

1. **VS Code Live Server 사용**
   ```bash
   # VS Code에서 프로젝트 열기
   # new-dark-main.html 우클릭
   # "Open with Live Server" 선택
   ```

2. **직접 브라우저에서 열기**
   ```
   # 파일 경로로 직접 접근
   file:///[경로]/new-dark-theme/new-dark-main.html
   ```

## 🌐 GitHub Pages 배포

- **배포 URL**: https://jskim-90.github.io/FIGMA_CONVERSION/
- **대시보드 직접 링크**: https://jskim-90.github.io/FIGMA_CONVERSION/conversion/new-dark-theme/new-dark-main.html

## 🛠️ 기술 스택

- **HTML5**: 시맨틱 마크업
- **CSS3**: CSS Variables, Grid, Flexbox
- **JavaScript**: Vanilla JS (ES6+)
- **디자인**: Figma Dev Mode MCP Server
- **폰트**: Pretendard, Tomorrow

## 📝 CSS 변수 활용

```css
/* 주요 색상 변수 */
:root {
  --bg-main: #0e1130;
  --bg-widget: rgba(14, 17, 48, 0.5);
  --border-primary: rgba(75, 92, 148, 0.5);
  --text-primary: #ffffff;
  --text-secondary: #a6b3de;
  --accent-purple: #7b4bff;
}
```

## 🎯 향후 계획

1. **반응형 디자인**: 다양한 화면 크기 지원
2. **데이터 연동**: 실시간 데이터 바인딩
3. **애니메이션**: 차트 및 전환 효과
4. **성능 최적화**: 리소스 로딩 최적화

## 📌 참고 링크

- [Figma 디자인](https://www.figma.com/design/VNqtXrH6ydqcDgYBsVFLbg/RENOBIT-ver.3.0-Design-System-Guide)
- [GitHub Repository](https://github.com/JSKIM-90/FIGMA_CONVERSION)