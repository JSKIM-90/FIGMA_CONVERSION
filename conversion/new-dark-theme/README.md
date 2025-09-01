# RENOBIT ver.3.0 Dark Theme Dashboard

## 📁 프로젝트 구조

```
new-dark-theme/
├── new-dark-main.html         # 메인 대시보드 HTML
├── components/                 # 재사용 컴포넌트
│   ├── header/                # 헤더 컴포넌트
│   ├── footer/                # 푸터 컴포넌트
│   ├── sidebar/               # 사이드바 컴포넌트
│   └── widgets/               # 위젯 컴포넌트
├── sections/                   # 섹션별 컴포넌트
│   ├── content01/             # 상단 섹션 (거래현황, 업무시스템, 계정)
│   ├── content02/             # 중간 섹션 (콜센터, 승인현황, TOP5)
│   └── browser-event/         # 하단 섹션 (이벤트현황)
├── charts/                     # 차트 컴포넌트
│   ├── donut/                 # 도넛 차트
│   ├── bar/                   # 막대 차트
│   ├── area/                  # 영역 차트
│   └── line/                  # 선 차트
├── tables/                     # 테이블 컴포넌트
│   ├── event-table/           # 이벤트 테이블
│   └── approval-table/        # 승인현황 테이블
├── assets/                     # 에셋 파일
│   ├── images/                # 이미지 파일
│   ├── icons/                 # 아이콘 파일
│   └── fonts/                 # 폰트 파일
├── styles/                     # 스타일시트
│   ├── main.css              # 메인 레이아웃
│   ├── dark-theme.css        # 다크 테마 변수
│   ├── components.css        # 컴포넌트 스타일
│   ├── charts.css            # 차트 스타일
│   └── tables.css            # 테이블 스타일
├── scripts/                    # JavaScript 파일
│   ├── main.js               # 메인 스크립트
│   ├── charts.js             # 차트 관련 스크립트
│   ├── tables.js             # 테이블 관련 스크립트
│   └── components.js         # 컴포넌트 스크립트
└── data/                       # 데이터 파일
    └── sample-data.json       # 샘플 데이터
```

## 🎨 Figma 구조 매핑

### Figma → 폴더 구조 매핑

| Figma 노드 | 폴더 위치 | 설명 |
|------------|----------|------|
| header (29:2449) | components/header/ | 상단 헤더 |
| content01 (29:2191) | sections/content01/ | 거래현황, 업무시스템, 계정 |
| ├─ section01 (29:2193) | charts/donut/ | 거래현황 도넛차트 |
| ├─ main-component (29:2428) | sections/content01/system-diagram/ | 업무시스템 현황 |
| └─ section02 (29:2226) | charts/bar/ | 계정 막대차트 |
| content02 (29:2046) | sections/content02/ | 콜센터, 승인현황, TOP5 |
| ├─ section01 (29:2047) | charts/area/ | 콜상담센터 영역차트 |
| ├─ section02 (29:2084) | tables/approval-table/ | 승인현황 테이블 |
| └─ section03 (29:2153) | sections/content02/top5/ | 서비스 TOP5 |
| browser-event (29:2033) | sections/browser-event/ | 이벤트현황 테이블 |

## 🚀 구현 순서

### Phase 1: 기본 구조 (완료)
- [x] 폴더 구조 생성
- [x] 메인 HTML 파일 생성
- [x] 기본 CSS 파일 생성
- [x] README 문서 작성

### Phase 2: 컴포넌트 개발 (진행 예정)
- [ ] Header 컴포넌트
- [ ] 거래현황 도넛차트
- [ ] 업무시스템현황 다이어그램
- [ ] 계정 막대차트
- [ ] 콜상담센터 영역차트
- [ ] 승인현황 테이블
- [ ] 서비스 TOP5 리스트
- [ ] 이벤트현황 테이블

### Phase 3: 통합 및 최적화
- [ ] 컴포넌트 통합
- [ ] 데이터 바인딩
- [ ] 애니메이션 추가
- [ ] 반응형 디자인
- [ ] 성능 최적화

## 🛠️ 기술 스택

- **HTML5**: 시맨틱 마크업
- **CSS3**: CSS Variables, Grid, Flexbox
- **JavaScript**: ES6+, 모듈 시스템
- **차트**: ECharts 5.4.3
- **테이블**: Tabulator 5.5.2
- **폰트**: Pretendard

## 📝 개발 가이드

### CSS 변수 활용
```css
/* Dark Theme Variables 사용 예시 */
.widget {
    background: var(--bg-widget);
    border: 1px solid var(--border-primary);
    color: var(--text-primary);
}
```

### 컴포넌트 로드 방식
```javascript
// 동적 컴포넌트 로딩
async function loadComponent(name, container) {
    const response = await fetch(`components/${name}/${name}.html`);
    const html = await response.text();
    document.querySelector(container).innerHTML = html;
}
```

### 차트 초기화
```javascript
// ECharts 초기화 예시
const chart = echarts.init(document.getElementById('chart-container'));
chart.setOption({
    // 차트 옵션
});
```

## 🎯 다음 단계

1. **Header 컴포넌트 구현**: Figma 헤더 디자인을 컴포넌트화
2. **차트 컴포넌트 개발**: 각 차트별 독립 모듈 생성
3. **테이블 구현**: Tabulator를 활용한 동적 테이블
4. **데이터 연동**: 실시간 데이터 바인딩
5. **최종 통합**: 모든 컴포넌트를 메인 페이지에 통합