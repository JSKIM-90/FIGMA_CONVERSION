# Figma 활용 팁 가이드

## 🎯 컴포넌트 간 관계 확인 방법

### 1. 스마트 거리 측정 (Smart Selection)
Figma에서 컴포넌트를 선택한 후 마우스를 움직이면 측정 기능이 활성화됩니다.

#### 작동 방식
1. **Alt/Option 키를 누른 상태**로 마우스를 다른 요소 위로 이동
2. **자동으로 표시되는 측정값**:
   - 선택한 요소와의 거리 (픽셀 단위)
   - 정렬 가이드라인
   - 여백과 간격

**Pro Tip**: Dev Mode에서는 측정값이 자동으로 표시됨

#### 확인 가능한 정보
- **Spacing**: 요소 간 여백 (margin, padding)
- **Dimensions**: 너비, 높이 차이
- **Alignment**: 정렬 상태 (좌측, 중앙, 우측)
- **Position**: 상대적 좌표 값


## 🔍 중첩된 컴포넌트 선택 방법

### 2. 계층 구조 탐색 (Layer Navigation)
Figma는 중첩된 요소들을 계층 구조로 관리합니다.

#### 선택 규칙
1. **부모 요소 우선 선택**
   - 첫 클릭: 가장 바깥쪽 부모 컨테이너 선택
   - 더블 클릭: 내부 자식 요소로 진입

2. **깊이 있는 선택**
   ```
   Frame (1st click)
   └── Group (2nd click/double-click)
       └── Text (3rd click/double-click)
   ```

3. **단축키 활용**
   - `Cmd/Ctrl + 클릭`: 깊은 요소 직접 선택
   - `Enter`: 선택된 그룹 내부로 진입
   - `Shift + Enter`: 부모 레벨로 나가기

#### Pro Tip
- **Layers 패널** 활용: 복잡한 구조에서는 레이어 패널에서 직접 선택이 더 효율적
- **Outline Mode** (`Cmd/Ctrl + Y`): 요소 구조를 명확히 보기

## 🔗 Figma URL을 통한 요소 직접 접근

### 3. Deep Link 활용법
Figma URL에는 특정 요소를 직접 가리키는 정보가 포함됩니다.

#### URL 구조
```
https://www.figma.com/design/[FILE_KEY]/[FILE_NAME]?node-id=[NODE_ID]
```

#### 활용 방법
1. **요소 선택 후 링크 복사**
   - 원하는 컴포넌트 선택
   - 우클릭 → "Copy link to selection"
   - 또는 `Cmd/Ctrl + L`

2. **브라우저에서 열기**
   - 복사한 링크를 주소창에 붙여넣기
   - 자동으로 해당 요소가 **선택된 상태**로 열림
   - 전체 디자인 컨텍스트에서 위치 확인 가능

3. **MCP 서버와 연동**
   - URL의 `node-id` 파라미터 추출
   - MCP 도구에 직접 전달 가능
   ```javascript
   // URL: ?node-id=203-1595
   mcp.get_code(nodeId: "203-1595")
   ```

#### 장점
- **협업 시 정확한 위치 공유**
- **버전 관리와 무관하게 요소 추적**
- **자동화 스크립트에서 활용 가능**

## 💡 추가 유용한 팁

### 4. 개발자를 위한 Figma 설정

#### Dev Mode 활성화
- 우측 상단 Toggle 스위치로 Dev Mode 전환
- CSS 코드, 측정값 자동 표시
- 에셋 다운로드 옵션 제공

#### 단위 설정
- Preferences → Units → "Pixels" 선택
- 웹 개발에 최적화된 px 단위 사용

### 5. 효율적인 검사 방법

#### Inspect Panel 활용
1. 우측 패널에서 "Inspect" 탭 선택
2. 확인 가능한 정보:
   - **Code**: CSS, iOS, Android 코드
   - **Colors**: 정확한 색상 값
   - **Typography**: 폰트 정보
   - **Effects**: 그림자, 블러 효과

#### 키보드 단축키 (검증됨)
- `Cmd/Ctrl + Shift + C`: 색상 복사
- `Cmd/Ctrl + \`: 레이어 패널 토글  
- `Shift + 1`: 전체 화면에 맞춤
- `Cmd/Ctrl + Alt + C`: CSS 코드 복사 (Dev Mode)
- `Cmd/Ctrl + Alt + S`: SVG 코드 복사 (Dev Mode)

## 📌 주의사항

1. **Auto Layout 이해**
   - Figma의 Auto Layout은 CSS Flexbox와 유사
   - 부모-자식 관계가 레이아웃에 영향

2. **Component vs Instance**
   - Main Component: 원본 (보라색 다이아몬드)
   - Instance: 복사본 (보라색 외곽선)
   - Instance 수정 시 원본은 영향받지 않음

3. **Export 설정 확인**
   - 요소 선택 → Export 패널
   - 배수 설정 (1x, 2x, 3x)
   - 포맷 선택 (PNG, JPG, SVG, PDF)

## 🔍 Dev Mode 전용 기능

### Dev Mode에서만 사용 가능한 기능들

#### 1. 코드 스니펫 자동 생성
- **CSS**: 선택한 요소의 스타일 코드
- **iOS/Android**: 네이티브 앱 코드
- **React**: JSX 컴포넌트 코드

#### 2. 에셋 다운로드 옵션
```
우측 패널 → Export
- SVG (코드 복사 가능)
- PNG (1x, 2x, 3x)
- PDF (벡터 유지)
```

#### 3. 디자인 토큰 추출
- 색상 변수
- 텍스트 스타일
- 이펙트 스타일
- 그리드 스타일

#### 4. 측정값 자동 표시
- 요소 간 거리
- 패딩과 마진
- 절대/상대 위치
- 컨테이너 관계

#### 5. Git 연동 (Beta)
- 버전 히스토리
- 브랜치 생성
- 변경사항 비교

## 🚀 워크플로우 최적화

### 개발자 친화적 Figma 사용법
1. **네이밍 컨벤션 설정**
   - 의미있는 레이어 이름 사용
   - BEM 방식 권장: `card__header--active`

2. **컴포넌트 구조화**
   - 재사용 가능한 단위로 분리
   - Variants 활용으로 상태 관리

3. **디자인 토큰 활용**
   - Colors, Text Styles, Effects를 변수로
   - 일관성 있는 디자인 시스템 구축

---

*이 가이드는 Figma를 활용한 효율적인 디자인-개발 협업을 위해 작성되었습니다.*

*Last updated: 2025-08-28*