# Figma to Code 가이드 (컴팩트 버전)

## 🚀 시작하기

### 필수 준비사항
1. Figma Desktop App + Dev Mode 활성화
2. MCP 서버 연결: `claude mcp add --transport sse figma-dev-mode-mcp-server http://127.0.0.1:3845/sse`

## 🎯 핵심 워크플로우

### 1️⃣ 필수: 4-Tool Set (항상 함께 호출!)
```javascript
const nodeId = "25:448";  // Figma에서 선택한 요소

// 반드시 4개 모두 호출
const [metadata, code, image, tokens] = await Promise.all([
  mcp__figma-dev-mode-mcp-server__get_metadata(nodeId),    // 크기, 좌표
  mcp__figma-dev-mode-mcp-server__get_code(nodeId),        // 구조, 스타일
  mcp__figma-dev-mode-mcp-server__get_image(nodeId),       // 시각적 확인
  mcp__figma-dev-mode-mcp-server__get_variable_defs(nodeId) // 디자인 토큰
]);
```

### 2️⃣ 구현 순서
1. **큰 레이아웃** → 섹션 → 컴포넌트 → 디테일
2. **Flexbox/Grid 우선**, absolute 최소화
3. **BEM 네이밍**, 시맨틱 HTML
4. **Figma 수치 100% 준수** (임의 수정 금지)

## 📊 역할 분담

| 사용자 | Claude |
|--------|--------|
| Figma에서 요소 선택 | 4-Tool Set 자동 호출 |
| "이 요소 구현해줘" | 정확한 수치 추출 & 코드 생성 |
| 결과 확인 & 피드백 | 즉시 수정 반영 |

## ⚠️ 주의사항

### Figma bg 프레임
- 1940x1100 같은 오버사이즈 프레임은 **무시**
- 실제 크기(1920x1080)만 구현

### 레이어 순서 (3D/겹침 디자인)
```html
<!-- Figma 레이어 순서와 반대로 배치 -->
<div class="layers">
  <img src="shape-1.svg">  <!-- Figma 맨 아래 = HTML 맨 위 -->
  <img src="shape-2.svg">
  <img src="shape-16.svg"> <!-- Figma 맨 위 = HTML 맨 아래 -->
</div>
```

## 🔑 핵심 원칙
- **MCP First, Manual Last**: 수동 측정은 최후의 수단
- **4-Tool Set is Mandatory**: 선택 아닌 필수
- **Pixel Perfect**: 1px도 타협 없음

## 📁 프로젝트 현황

### 구현 완료 컴포넌트
- ✅ Header (node-id: 25:864)
- ✅ Content01 (거래현황, 트랜젝션, 업무시스템)
- ✅ Content02 (콜센터, 승인, TOP5)
- 🟡 Browser Event (작업 중)

### 주요 학습 사항
1. **CSS Transform과 Figma 좌표**: rotate 시 좌표 재계산 필요
2. **Figma HUG ≠ line-height**: 명시적 px 설정 필요
3. **BEM > 중첩 선택자**: 유지보수성 우선

## 🎯 실제 사용 예시

```javascript
// ❌ 잘못된 방법 (수동)
.header {
  width: 1860px;  // "아마 이 정도?"
}

// ✅ 올바른 방법 (MCP)
const metadata = await get_metadata("25:864");
// { width: 1860, height: 75, x: 29, y: -1 }

.header {
  width: ${metadata.width}px;   // 1860px (정확)
  height: ${metadata.height}px;  // 75px (정확)
}
```

## 📚 관련 문서
- 상세 가이드: `CLAUDE.md`
- 구현 예제: `conversion/` 폴더
- 리뷰: 각 프로젝트별 `REVIEW.md`

---
**Remember**: MCP로 정확도는 높이고, 작업 시간은 줄이고!