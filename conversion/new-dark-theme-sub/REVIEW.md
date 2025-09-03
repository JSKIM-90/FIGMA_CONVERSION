# new-dark-theme-sub 레이아웃 검토 결과

## 📅 검토일: 2025-01-03

## ✅ 개선된 점 (new-dark-theme-main 대비)

1. **Flexbox 기반 레이아웃**
   ```css
   .dashboard-wrap {
     display: flex;
     flex-direction: column;
     gap: 15px;
   }
   ```
   - Figma to Code 원칙 준수: "Flexbox/Grid 우선"
   - absolute positioning 크게 감소

2. **BEM 네이밍 컨벤션**
   - 체계적인 클래스 구조 (header__bg, header__logo 등)
   - 컴포넌트 기반 스타일링

3. **Semantic HTML**
   - `<header>`, `<nav>`, `<section>` 태그 사용
   - 접근성과 SEO 향상

4. **컴포넌트 구조 개선**
   - 섹션별로 명확한 분리
   - gap 속성으로 간격 관리

## ❌ 여전히 개선 필요한 부분

### 1. 1940x1100 wrapper 문제
```css
.dashboard-wrap {
  width: 1940px;
  height: 1100px;
  margin: -10px;  /* 음수 마진 hack */
}
```

**문제점**:
- bg 프레임 개념이 여전히 남아있음
- 음수 마진으로 해결하는 것은 안티패턴
- CLAUDE.md 원칙: "bg는 Figma 디자인 도구용, 퍼블리싱 시 무시"

### 2. 고정 크기 컨테이너
```css
.dashboard-container {
  width: 1920px;
  height: 1080px;
}
```

**문제점**: 
- 반응형 대응 불가
- 다양한 화면 크기 미고려

### 3. position: relative 남용
```css
.content01 .section01 {
  position: relative;
}
```

**문제점**:
- 모든 섹션에 position: relative
- 내부 요소들이 absolute일 가능성 높음

## 🔧 개선 제안

1. **Wrapper 단순화**
   ```css
   .dashboard-container {
     width: 100%;
     max-width: 1920px;
     min-height: 1080px;
     padding: 0 39px;
   }
   ```

2. **불필요한 position 제거**
   - relative는 꼭 필요한 경우만
   - 대부분 normal flow로 해결 가능

3. **CSS 변수 도입**
   ```css
   :root {
     --gap-section: 28px;
     --gap-component: 31px;
     --padding-container: 39px;
   }
   ```

## 📊 평가
- **작동 여부**: ✅ 정상 작동
- **원칙 준수**: ✅ 상당 부분 준수 (75%)
- **유지보수성**: ⚠️ 양호하나 개선 여지 있음
- **확장성**: ⚠️ 보통

## 🎯 우선순위
1. 🔴 **높음**: 1940x1100 wrapper 제거
2. 🟡 **중간**: 불필요한 position: relative 제거
3. 🟢 **낮음**: CSS 변수 도입

## 💡 총평
new-dark-theme-main보다 확실히 개선됨. Flexbox 사용과 BEM 네이밍이 좋음.
하지만 여전히 Figma의 bg 프레임 개념을 그대로 구현한 것은 개선 필요.