# Figma to Tabulator 변환 가이드

## 🎯 개요
Figma의 테이블 디자인을 Tabulator.js 옵션으로 변환하는 방법을 설명합니다.

## 📚 Tabulator 소개
Tabulator는 강력한 인터랙티브 테이블 라이브러리로, 정렬, 필터링, 페이지네이션, 편집 등 다양한 기능을 제공합니다.

### 설치 방법
```html
<!-- CDN (v5.5) -->
<link href="https://unpkg.com/tabulator-tables@5.5.2/dist/css/tabulator.min.css" rel="stylesheet">
<script type="text/javascript" src="https://unpkg.com/tabulator-tables@5.5.2/dist/js/tabulator.min.js"></script>

<!-- NPM -->
npm install tabulator-tables

<!-- Import -->
import {Tabulator} from 'tabulator-tables';
```

## 🔄 변환 가능한 요소들

### 1. 테이블 스타일 매핑
```javascript
// Figma 테이블 스타일 → Tabulator 옵션
const figmaTableStyle = {
  backgroundColor: '#122023',
  borderColor: '#444444',
  borderWidth: 1,
  borderRadius: 8,
  padding: 16,
  cellSpacing: 0
};

// Tabulator 변환
const tabulatorOptions = {
  layout: "fitColumns",
  responsiveLayout: "hide",
  height: "100%",
  // 테이블 스타일 적용
  cssClass: "custom-table-style"
};

// CSS 커스터마이징
const tableStyles = `
  .custom-table-style {
    background-color: ${figmaTableStyle.backgroundColor};
    border: ${figmaTableStyle.borderWidth}px solid ${figmaTableStyle.borderColor};
    border-radius: ${figmaTableStyle.borderRadius}px;
    padding: ${figmaTableStyle.padding}px;
  }
`;
```

### 2. 컬럼 헤더 스타일
```javascript
// Figma 헤더 디자인
const figmaHeader = {
  backgroundColor: '#1a2b3c',
  textColor: '#ffffff',
  fontSize: 14,
  fontWeight: 600,
  padding: '12px 16px',
  borderBottom: '2px solid #d9f460',
  textAlign: 'left'
};

// Tabulator 컬럼 정의
const columns = [
  {
    title: "이벤트 유형",
    field: "eventType",
    headerSort: true,
    headerHozAlign: figmaHeader.textAlign,
    cssClass: "custom-header",
    formatter: function(cell) {
      // Figma 스타일 적용
      const el = cell.getElement();
      el.style.fontSize = `${figmaHeader.fontSize}px`;
      el.style.fontWeight = figmaHeader.fontWeight;
      return cell.getValue();
    }
  }
];

// 헤더 CSS
const headerStyles = `
  .tabulator-header {
    background-color: ${figmaHeader.backgroundColor} !important;
    border-bottom: ${figmaHeader.borderBottom} !important;
  }
  .tabulator-col {
    background-color: transparent !important;
    border-right: 1px solid rgba(255,255,255,0.1) !important;
  }
  .tabulator-col-content {
    padding: ${figmaHeader.padding} !important;
  }
  .tabulator-col-title {
    color: ${figmaHeader.textColor} !important;
    font-size: ${figmaHeader.fontSize}px !important;
    font-weight: ${figmaHeader.fontWeight} !important;
  }
`;
```

### 3. 행(Row) 스타일링
```javascript
// Figma 행 디자인
const figmaRow = {
  height: 48,
  backgroundColor: '#ffffff',
  hoverColor: '#f5f5f5',
  selectedColor: '#e8f5e9',
  borderBottom: '1px solid #e0e0e0',
  alternateColor: '#fafafa'
};

// Tabulator 행 옵션
const rowOptions = {
  rowHeight: figmaRow.height,
  selectable: true,
  selectableRangeMode: "click",
  
  // 행 포맷터
  rowFormatter: function(row) {
    const rowEl = row.getElement();
    const rowIndex = row.getIndex();
    
    // 짝수/홀수 행 스타일
    if (rowIndex % 2 === 0) {
      rowEl.style.backgroundColor = figmaRow.alternateColor;
    }
    
    // 호버 효과
    rowEl.addEventListener('mouseenter', () => {
      rowEl.style.backgroundColor = figmaRow.hoverColor;
    });
    
    rowEl.addEventListener('mouseleave', () => {
      rowEl.style.backgroundColor = rowIndex % 2 === 0 ? 
        figmaRow.alternateColor : figmaRow.backgroundColor;
    });
  },
  
  // 선택 시 스타일
  rowSelected: function(row) {
    row.getElement().style.backgroundColor = figmaRow.selectedColor;
  },
  
  rowDeselected: function(row) {
    const rowIndex = row.getIndex();
    row.getElement().style.backgroundColor = rowIndex % 2 === 0 ? 
      figmaRow.alternateColor : figmaRow.backgroundColor;
  }
};
```

### 4. 셀(Cell) 스타일링
```javascript
// Figma 셀 디자인
const figmaCell = {
  padding: '12px 16px',
  fontSize: 14,
  textColor: '#323232',
  mutedTextColor: '#999999',
  alignment: 'left',
  verticalAlignment: 'middle'
};

// Tabulator 셀 포맷터
const cellFormatters = {
  // 상태 뱃지 포맷터
  statusBadge: function(cell) {
    const value = cell.getValue();
    const badgeColors = {
      critical: { bg: 'rgba(243, 47, 80, 0.1)', color: '#f43050', border: '#f43050' },
      warning: { bg: 'rgba(255, 152, 0, 0.1)', color: '#ff9800', border: '#ff9800' },
      normal: { bg: 'rgba(76, 175, 80, 0.1)', color: '#4caf50', border: '#4caf50' },
      minor: { bg: 'rgba(33, 150, 243, 0.1)', color: '#2196f3', border: '#2196f3' }
    };
    
    const style = badgeColors[value.toLowerCase()] || badgeColors.normal;
    
    return `<span style="
      display: inline-block;
      padding: 4px 12px;
      background: ${style.bg};
      color: ${style.color};
      border: 1px solid ${style.border};
      border-radius: 15px;
      font-size: 12px;
      font-weight: 500;
    ">${value}</span>`;
  },
  
  // 숫자 포맷터
  numberFormat: function(cell) {
    const value = cell.getValue();
    return `<span style="
      color: ${figmaCell.textColor};
      font-size: ${figmaCell.fontSize}px;
      font-family: 'Pretendard', sans-serif;
    ">${value.toLocaleString()}</span>`;
  },
  
  // 날짜 포맷터
  dateFormat: function(cell) {
    const value = cell.getValue();
    const date = new Date(value);
    const formatted = date.toLocaleDateString('ko-KR', {
      year: 'numeric',
      month: '2-digit',
      day: '2-digit'
    });
    
    return `<span style="
      color: ${figmaCell.mutedTextColor};
      font-size: ${figmaCell.fontSize}px;
    ">${formatted}</span>`;
  }
};

// 셀 CSS
const cellStyles = `
  .tabulator-cell {
    padding: ${figmaCell.padding} !important;
    font-size: ${figmaCell.fontSize}px !important;
    color: ${figmaCell.textColor} !important;
    vertical-align: ${figmaCell.verticalAlignment} !important;
    border-right: 1px solid rgba(0,0,0,0.05) !important;
  }
`;
```

### 5. 인터랙션 효과
```javascript
// Figma 인터랙션 디자인
const figmaInteractions = {
  sortIcon: '▼',
  filterIcon: '🔍',
  resizeHandle: true,
  clickToEdit: true,
  contextMenu: true
};

// Tabulator 인터랙션 옵션
const interactionOptions = {
  // 정렬
  headerSort: true,
  headerSortElement: "<span style='margin-left: 5px; opacity: 0.5;'>▼</span>",
  
  // 필터
  headerFilter: true,
  headerFilterPlaceholder: "필터...",
  
  // 컬럼 리사이즈
  resizableColumns: figmaInteractions.resizeHandle,
  
  // 편집
  cellEdited: function(cell) {
    // 편집 후 스타일 애니메이션
    const el = cell.getElement();
    el.style.transition = "background-color 0.3s";
    el.style.backgroundColor = "#d9f460";
    setTimeout(() => {
      el.style.backgroundColor = "";
    }, 300);
  },
  
  // 컨텍스트 메뉴
  rowContextMenu: [
    {
      label: "상세보기",
      action: function(e, row) {
        console.log("상세보기:", row.getData());
      }
    },
    {
      label: "수정",
      action: function(e, row) {
        // 편집 모드 활성화
        row.getTable().navigateToRow(row);
      }
    },
    {
      separator: true
    },
    {
      label: "삭제",
      action: function(e, row) {
        row.delete();
      }
    }
  ]
};
```

## 🛠️ 변환 워크플로우

### 1단계: Figma에서 테이블 디자인 분석
```javascript
// MCP 도구로 테이블 정보 추출
async function extractTableDesign(nodeId) {
  const tableDesign = await mcp.get_code(nodeId);
  const designTokens = await mcp.get_variable_defs(nodeId);
  const tableImage = await mcp.get_image(nodeId);
  
  return {
    structure: analyzeTableStructure(tableDesign),
    styles: extractTableStyles(designTokens),
    preview: tableImage
  };
}
```

### 2단계: 디자인 토큰 추출
```javascript
function extractTableTokens(figmaData) {
  return {
    // 색상 시스템
    colors: {
      headerBg: figmaData.headerBackground,
      rowBg: figmaData.rowBackground,
      rowHover: figmaData.rowHoverColor,
      border: figmaData.borderColor,
      text: figmaData.textColor,
      textMuted: figmaData.mutedTextColor
    },
    
    // 간격 시스템
    spacing: {
      cellPadding: figmaData.cellPadding,
      headerPadding: figmaData.headerPadding,
      rowHeight: figmaData.rowHeight
    },
    
    // 타이포그래피
    typography: {
      fontFamily: figmaData.fontFamily || 'Pretendard',
      fontSize: figmaData.fontSize || 14,
      headerFontSize: figmaData.headerFontSize || 14,
      fontWeight: figmaData.fontWeight || 400
    },
    
    // 테이블 특성
    tableSpecific: {
      borderRadius: figmaData.borderRadius,
      stripedRows: figmaData.hasAlternateRows,
      showGridLines: figmaData.showGridLines
    }
  };
}
```

### 3단계: Tabulator 옵션 생성
```javascript
function generateTabulatorOptions(tokens, columns, data) {
  return {
    // 데이터
    data: data,
    columns: generateColumns(columns, tokens),
    
    // 레이아웃
    layout: "fitColumns",
    responsiveLayout: "hide",
    height: "auto",
    minHeight: 300,
    maxHeight: 600,
    
    // 스타일링
    rowHeight: tokens.spacing.rowHeight,
    headerHeight: tokens.spacing.rowHeight * 1.2,
    
    // 인터랙션
    movableColumns: true,
    movableRows: true,
    resizableColumns: true,
    
    // 정렬 & 필터
    headerSort: true,
    headerFilter: true,
    
    // 선택
    selectable: true,
    selectableRangeMode: "click",
    
    // 페이지네이션
    pagination: "local",
    paginationSize: 20,
    paginationSizeSelector: [10, 20, 50, 100],
    
    // 행 포맷터
    rowFormatter: function(row) {
      applyRowStyles(row, tokens);
    },
    
    // 초기화 완료 콜백
    tableBuilt: function() {
      applyCustomStyles(this.element, tokens);
    }
  };
}
```

## 📊 실제 구현 예시

### 이벤트 대시보드 테이블
```javascript
// Figma 이벤트 테이블 디자인을 Tabulator로 변환
class FigmaEventTable {
  constructor(containerId) {
    this.containerId = containerId;
    this.table = null;
  }
  
  async init(figmaNodeId) {
    // Figma 디자인 가져오기
    const design = await this.fetchFigmaDesign(figmaNodeId);
    const tokens = this.extractTokens(design);
    
    // Tabulator 인스턴스 생성
    this.table = new Tabulator(`#${this.containerId}`, {
      columns: this.generateColumns(tokens),
      data: this.sampleData,
      ...this.generateOptions(tokens)
    });
    
    // 커스텀 스타일 적용
    this.applyCustomStyles(tokens);
  }
  
  generateColumns(tokens) {
    return [
      {
        title: "시간",
        field: "timestamp",
        width: 150,
        formatter: "datetime",
        formatterParams: {
          outputFormat: "YYYY-MM-DD HH:mm:ss"
        }
      },
      {
        title: "이벤트 유형",
        field: "type",
        width: 120,
        formatter: function(cell) {
          const value = cell.getValue();
          const colors = {
            Critical: "#f43050",
            Warning: "#ff9800",
            Minor: "#2196f3",
            Normal: "#4caf50"
          };
          
          return `<span style="
            padding: 4px 12px;
            background: ${colors[value]}20;
            color: ${colors[value]};
            border: 1px solid ${colors[value]};
            border-radius: 15px;
            font-size: 12px;
            font-weight: 500;
          ">${value}</span>`;
        }
      },
      {
        title: "시스템",
        field: "system",
        width: 150
      },
      {
        title: "메시지",
        field: "message",
        formatter: "textarea",
        variableHeight: true
      },
      {
        title: "상태",
        field: "status",
        width: 100,
        formatter: function(cell) {
          const value = cell.getValue();
          const icon = value === "Active" ? "🔴" : "🟢";
          return `${icon} ${value}`;
        }
      }
    ];
  }
  
  generateOptions(tokens) {
    return {
      // 테이블 스타일
      height: 400,
      layout: "fitColumns",
      
      // 헤더 스타일
      headerHeight: 48,
      columnDefaults: {
        headerSort: true,
        headerFilter: true,
        headerFilterPlaceholder: "검색..."
      },
      
      // 행 스타일
      rowHeight: 52,
      selectable: 1,
      
      // 인터랙션
      movableColumns: true,
      resizableColumns: true,
      
      // 가상 DOM (성능 최적화)
      virtualDom: true,
      virtualDomBuffer: 300,
      
      // 행 포맷터
      rowFormatter: function(row) {
        const rowEl = row.getElement();
        const data = row.getData();
        
        // 이벤트 타입별 행 스타일
        if (data.type === "Critical") {
          rowEl.style.borderLeft = "3px solid #f43050";
          rowEl.style.backgroundColor = "rgba(243, 47, 80, 0.05)";
        }
      }
    };
  }
  
  applyCustomStyles(tokens) {
    const styles = `
      /* Figma 디자인 스타일 적용 */
      #${this.containerId} .tabulator {
        background-color: ${tokens.colors.background};
        border: 1px solid ${tokens.colors.border};
        border-radius: 8px;
        font-family: ${tokens.typography.fontFamily};
      }
      
      #${this.containerId} .tabulator-header {
        background-color: ${tokens.colors.headerBg};
        border-bottom: 2px solid ${tokens.colors.accent};
      }
      
      #${this.containerId} .tabulator-row {
        border-bottom: 1px solid ${tokens.colors.border}20;
      }
      
      #${this.containerId} .tabulator-row:hover {
        background-color: ${tokens.colors.rowHover};
      }
      
      #${this.containerId} .tabulator-row.tabulator-selected {
        background-color: ${tokens.colors.selected};
      }
    `;
    
    // 스타일 주입
    const styleEl = document.createElement('style');
    styleEl.textContent = styles;
    document.head.appendChild(styleEl);
  }
  
  // 샘플 데이터
  sampleData = [
    {
      timestamp: new Date(),
      type: "Critical",
      system: "콜센터",
      message: "서버 응답 시간 초과",
      status: "Active"
    },
    {
      timestamp: new Date(),
      type: "Warning",
      system: "은행",
      message: "메모리 사용량 80% 도달",
      status: "Active"
    },
    {
      timestamp: new Date(),
      type: "Normal",
      system: "MCA",
      message: "정상 작동 중",
      status: "Resolved"
    }
  ];
}

// 사용 예시
const eventTable = new FigmaEventTable('event-table-container');
eventTable.init('figma-node-id-123:456');
```

## 🎨 고급 변환 기법

### 1. 반응형 테이블
```javascript
// Figma 반응형 디자인 → Tabulator
const responsiveOptions = {
  responsiveLayout: "collapse",
  responsiveLayoutCollapseFormatter: function(data) {
    // 모바일 뷰 커스텀 포맷
    let html = "<div style='padding: 10px;'>";
    data.forEach(col => {
      html += `<div style='margin: 5px 0;'>
        <strong>${col.title}:</strong> ${col.value}
      </div>`;
    });
    html += "</div>";
    return html;
  }
};
```

### 2. 트리 구조 테이블
```javascript
// Figma 계층 구조 → Tabulator Tree
const treeOptions = {
  dataTree: true,
  dataTreeStartExpanded: true,
  dataTreeChildIndent: 20,
  dataTreeChildField: "children",
  dataTreeCollapseElement: "<span>▼</span>",
  dataTreeExpandElement: "<span>▶</span>"
};
```

### 3. 편집 가능 테이블
```javascript
// Figma 입력 필드 → Tabulator Editor
const editableColumns = [
  {
    title: "이름",
    field: "name",
    editor: "input",
    validator: ["required", "string"],
    cellEdited: function(cell) {
      // 저장 애니메이션
      cell.getElement().style.backgroundColor = "#d9f460";
      setTimeout(() => {
        cell.getElement().style.backgroundColor = "";
      }, 300);
    }
  },
  {
    title: "상태",
    field: "status",
    editor: "select",
    editorParams: {
      values: ["Active", "Inactive", "Pending"]
    }
  }
];
```

## 💡 활용 시나리오

1. **대시보드 데이터 테이블**
   - Figma로 디자인한 대시보드 테이블을 인터랙티브 Tabulator로 변환
   
2. **관리자 패널**
   - Figma 관리자 UI 디자인을 완전한 CRUD 기능 테이블로 구현
   
3. **리포트 테이블**
   - Figma 리포트 레이아웃을 인쇄/내보내기 가능한 테이블로 변환

## 🚀 자동화 스크립트

```javascript
class FigmaToTabulator {
  constructor(mcpServer) {
    this.mcp = mcpServer;
  }
  
  async convert(figmaNodeId, options = {}) {
    // 1. Figma 테이블 디자인 추출
    const design = await this.mcp.get_code(figmaNodeId);
    const tokens = await this.mcp.get_variable_defs(figmaNodeId);
    const preview = await this.mcp.get_image(figmaNodeId);
    
    // 2. 테이블 구조 분석
    const tableStructure = this.analyzeTableStructure(design);
    
    // 3. Tabulator 옵션 생성
    const tabulatorConfig = this.generateConfig(
      tableStructure,
      tokens,
      options
    );
    
    // 4. 스타일시트 생성
    const styles = this.generateStyles(tokens);
    
    // 5. 초기화 코드 생성
    const initCode = this.generateInitCode(tabulatorConfig);
    
    return {
      config: tabulatorConfig,
      styles: styles,
      initCode: initCode,
      preview: preview
    };
  }
  
  analyzeTableStructure(design) {
    // 테이블 구조 분석 로직
    return {
      columns: this.extractColumns(design),
      rows: this.extractRows(design),
      hasHeader: true,
      hasFooter: false,
      isStriped: this.detectStripedPattern(design)
    };
  }
  
  generateConfig(structure, tokens, options) {
    const baseConfig = {
      layout: options.layout || "fitColumns",
      height: options.height || "auto",
      columns: this.mapColumnsToTabulator(structure.columns, tokens),
      ...this.mapStylesToOptions(tokens)
    };
    
    // 옵션 병합
    return { ...baseConfig, ...options };
  }
  
  generateStyles(tokens) {
    return `
      .tabulator {
        font-family: ${tokens.fontFamily};
        background-color: ${tokens.backgroundColor};
        border: 1px solid ${tokens.borderColor};
        border-radius: ${tokens.borderRadius}px;
      }
      
      .tabulator-header {
        background-color: ${tokens.headerBackground};
        border-bottom: 2px solid ${tokens.accentColor};
      }
      
      .tabulator-cell {
        padding: ${tokens.cellPadding};
        color: ${tokens.textColor};
      }
    `;
  }
  
  generateInitCode(config) {
    return `
      // Tabulator 초기화
      const table = new Tabulator("#table-container", ${
        JSON.stringify(config, null, 2)
      });
      
      // 데이터 로드
      table.setData(tableData);
      
      // 이벤트 리스너
      table.on("rowClick", function(e, row) {
        console.log("Row clicked:", row.getData());
      });
    `;
  }
}

// 사용 예시
const converter = new FigmaToTabulator(mcpServer);
const result = await converter.convert('node-123:456', {
  pagination: true,
  paginationSize: 20,
  selectable: true
});

console.log(result.initCode);
```

## 📝 주의사항

- Figma는 정적 디자인, Tabulator는 동적 테이블
- 모든 시각적 효과가 1:1로 변환되지 않을 수 있음
- 데이터 바인딩과 이벤트 핸들링은 추가 구현 필요
- 브라우저 호환성 확인 필요
- 대용량 데이터 처리 시 가상 DOM 옵션 활용

## 📚 참고 자료

- [Tabulator 공식 문서](https://tabulator.info/)
- [Tabulator 예제](https://tabulator.info/examples)
- [Figma API 문서](https://www.figma.com/developers/api)
- [MCP 프로토콜 문서](https://modelcontextprotocol.io)