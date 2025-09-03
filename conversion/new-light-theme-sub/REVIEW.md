# new-light-theme-sub 레이아웃 검토 결과

## 📅 검토일: 2025-01-03

## 📋 요약
**new-dark-theme-sub와 거의 동일한 구조**
- 동일한 layout.css 구조
- 동일한 BEM 네이밍
- 차이점: 배경색만 다름 (#0E1130 → #E8EDFF)

## ✅ 잘 구현된 부분

1. **Flexbox 기반 레이아웃**
   ```css
   .dashboard-wrap {
     display: flex;
     flex-direction: column;
     gap: 15px;
   }
   ```
   - Figma to Code 원칙 준수: "Flexbox/Grid 우선"

2. **BEM 네이밍 컨벤션**
   - header__bg, header__logo 등
   - 체계적인 클래스 구조

3. **Semantic HTML**
   - `<header>`, `<nav>`, `<section>` 사용
   - 접근성 향상

4. **컴포넌트 구조**
   - 섹션별 명확한 분리
   - gap으로 간격 관리

## ❌ 개선 필요한 부분

### 1. 1940x1100 wrapper 문제
```css
.dashboard-wrap {
  width: 1940px;
  height: 1100px;
  margin: -10px;  /* 음수 마진 hack */
}
```

**문제점**:
- bg 프레임 개념 그대로 구현
- 음수 마진 안티패턴
- CLAUDE.md 원칙 위반: "bg는 퍼블리싱 시 무시"

### 2. 고정 크기
```css
.dashboard-container {
  width: 1920px;
  height: 1080px;
}
```

**문제점**:
- 반응형 대응 불가

### 3. position: relative 남용
```css
.content01 .section01 {
  position: relative;
}
```

**문제점**:
- 모든 섹션에 불필요한 position: relative

## 🔧 개선 제안

### Dark/Light 테마 통합
```css
/* 공통 layout.css */
.dashboard-container {
  /* 공통 레이아웃 */
}

/* theme-dark.css */
.theme-dark {
  --bg-color: #0E1130;
}

/* theme-light.css */
.theme-light {
  --bg-color: #E8EDFF;
}
```

### Wrapper 제거
```css
.dashboard-container {
  width: 100%;
  max-width: 1920px;
  padding: 9px 39px;
  /* wrapper 없이 직접 처리 */
}
```

## 📊 평가
- **작동 여부**: ✅ 정상 작동
- **원칙 준수**: ✅ 상당 부분 준수 (75%)
- **유지보수성**: ⚠️ dark-theme-sub와 중복 코드
- **확장성**: ⚠️ 테마 시스템 필요

## 🎯 우선순위
1. 🔴 **높음**: dark/light 테마 통합 시스템
2. 🟡 **중간**: 1940x1100 wrapper 제거
3. 🟢 **낮음**: CSS 변수로 테마 관리

## 💡 총평
new-dark-theme-sub와 코드가 거의 동일하여 **코드 중복이 심함**.
테마 시스템을 도입하여 하나의 코드베이스로 관리하는 것이 필요.