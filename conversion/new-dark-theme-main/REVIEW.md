# new-dark-theme-main 레이아웃 검토 결과

## 📅 검토일: 2025-01-03

## ✅ 잘 지켜진 원칙들

1. **정확한 Figma 좌표 반영**
   - 모든 요소에 Figma 주석과 정확한 px 값 사용
   - node-id 표시 (data-node-id 속성)

2. **디버깅 시각화**
   - hover 효과로 각 섹션 경계 표시
   - 색상별 구분으로 계층 구조 명확

3. **컴포넌트 분리**
   - CSS 파일이 컴포넌트별로 잘 분리됨
   - 구조화된 폴더 구성

## ❌ 개선이 필요한 부분

### 1. Absolute Positioning 과도 사용
```css
/* 현재: 모든 메인 섹션이 absolute */
#header { position: absolute; }
#content01 { position: absolute; }
#content02 { position: absolute; }
#browser-event { position: absolute; }
```

**문제점**: 
- Figma to Code 원칙 위반: "Flexbox/Grid 우선, absolute 최소화"
- 유지보수 어려움
- 반응형 대응 불가능

### 2. bg 프레임 불필요하게 구현
```css
#bg {
  top: -10px;
  left: -10px;
  width: 1940px;
  height: 1100px;
}
```

**문제점**:
- CLAUDE.md 원칙: "bg는 Figma 디자인 도구용, 퍼블리싱 시 무시"
- 불필요한 오버사이즈 배경으로 복잡도 증가

### 3. 고정 크기 컨테이너
```css
#new-dark-main {
  width: 1920px;  /* 고정 */
  height: 1080px; /* 고정 */
}
```

**문제점**: 
- 반응형 대응 불가
- 다양한 화면 크기 고려 안 됨

### 4. CSS 변수 미사용
- 색상, 간격 등이 하드코딩됨
- 디자인 토큰 활용 안 됨

## 🔧 개선 방향

1. **레이아웃 구조 개선**
   - Normal Flow 활용 (flexbox/grid)
   - absolute는 꼭 필요한 경우만

2. **bg 프레임 제거**
   - 메인 컨테이너에 직접 배경색 적용

3. **CSS 변수 도입**
   ```css
   :root {
     --color-bg-primary: #0E1130;
     --spacing-container: 29px;
     --width-content: 1860px;
   }
   ```

4. **반응형 고려**
   - 미디어 쿼리 추가
   - max-width 활용

## 📊 평가
- **작동 여부**: ✅ 정상 작동
- **원칙 준수**: ⚠️ 부분적 준수 (60%)
- **유지보수성**: ⚠️ 개선 필요
- **확장성**: ❌ 낮음

## 🎯 우선순위
1. 🔴 **높음**: bg 프레임 제거
2. 🟡 **중간**: absolute → flexbox 전환
3. 🟢 **낮음**: CSS 변수 도입