# MCP (Model Context Protocol) 이해하기

## 📌 MCP의 본질

MCP는 **서로 다른 애플리케이션 간의 맥락(Context)을 이해하는 표준 통신 규약**입니다.

단순히 데이터를 전달하는 것이 아니라, 그 데이터가 가진 **의미와 맥락**을 함께 전달합니다.

## 🔄 작동 원리

```
[원본 앱]     →    [MCP Server]    →    [표준 포맷]    →    [외부 앱]    →    [새 컨텐츠]
   ↓                    ↓                   ↓                  ↓                 ↓
앱 고유 데이터      포맷 변환          JSON/통신규약       데이터 해석      새로운 결과물
(Figma 디자인)    (로컬 서버)         (컨텍스트)        (Claude)        (HTML/CSS)
```

## 💡 왜 "Context Protocol"인가?

### 1. 컨텍스트(맥락) 전달
- 단순 데이터가 아닌 **의미있는 정보** 전달
- 디자인의 의도, 구조, 관계성을 포함

### 2. 의미 보존
- 원본 앱의 **의도**가 손실되지 않음
- 데이터의 **구조적 의미**가 유지됨

### 3. 상호 이해
- 서로 다른 앱이 **같은 맥락**을 공유
- 표준화된 방식으로 **의미를 해석**

## 🎯 실제 예시: Figma → Claude Code

### 1. Figma가 제공하는 컨텍스트
```javascript
{
  "nodeId": "197:584",
  "type": "BUTTON",
  "name": "PrimaryButton",
  "style": {
    "backgroundColor": "#d9f460",
    "padding": {
      "top": 8,
      "right": 16,
      "bottom": 8,
      "left": 16
    },
    "borderRadius": 4,
    "fontSize": 14,
    "fontWeight": 600
  },
  "children": [{
    "type": "TEXT",
    "content": "종합현황"
  }],
  "constraints": {
    "horizontal": "CENTER",
    "vertical": "CENTER"
  },
  "interactions": [{
    "trigger": "ON_CLICK",
    "action": "NAVIGATE"
  }]
}
```

### 2. Claude가 이해하고 생성하는 결과
```html
<button class="primary-button">
  종합현황
</button>
```

```css
.primary-button {
  background-color: #d9f460;
  padding: 8px 16px;
  border-radius: 4px;
  font-size: 14px;
  font-weight: 600;
  cursor: pointer;
  border: none;
}
```

## 🔧 MCP 설정의 의미

### Claude Code에서의 설정
```bash
claude mcp add --transport sse figma-dev-mode-mcp-server http://127.0.0.1:3845/sse
```

이 명령어가 하는 일:
1. **연결 설정**: Claude Code와 Figma MCP Server 연결
2. **프로토콜 정의**: SSE(Server-Sent Events) 통신 방식 사용
3. **엔드포인트 등록**: 로컬 서버 주소 등록
4. **컨텍스트 파이프라인 구축**: Figma → MCP → Claude 데이터 흐름 생성

## 🌟 MCP의 가치

### 1. 통역사 역할
- 단순 번역이 아닌 **문화적 맥락**까지 전달
- 앱 간의 **의미있는 대화** 가능

### 2. 표준화의 힘
- 모든 앱이 **같은 언어**로 소통
- 새로운 앱도 쉽게 **생태계에 참여**

### 3. AI와의 시너지
- AI가 **맥락을 이해**하고 활용
- 더 정확하고 의미있는 **결과물 생성**

## 📚 핵심 개념 정리

| 개념 | 설명 |
|------|------|
| **Context** | 데이터가 가진 의미와 맥락 |
| **Protocol** | 표준화된 통신 규약 |
| **Server** | 앱의 데이터를 표준 포맷으로 변환하는 로컬 서버 |
| **Client** | MCP 데이터를 받아 활용하는 외부 애플리케이션 |
| **Transport** | 데이터 전송 방식 (SSE, WebSocket 등) |

## 🎨 Figma MCP의 특별함

Figma MCP는 특히 **디자인의 시각적 맥락**을 코드로 변환하는데 특화되어 있습니다:

- **레이아웃 의도**: Flexbox, Grid 등 구조적 의미
- **디자인 토큰**: 색상, 간격, 타이포그래피 시스템
- **컴포넌트 관계**: 재사용성과 일관성
- **인터랙션 정보**: 사용자 행동과 반응

## 💭 기억해야 할 것

> MCP는 단순한 데이터 전달이 아닌, **맥락을 이해하는 소통**입니다.
> 
> 서로 다른 세계(앱)가 같은 언어(프로토콜)로 대화하며,
> AI가 그 대화를 이해하고 새로운 가치를 창출합니다.

---

*"Context is King in the age of AI collaboration"*