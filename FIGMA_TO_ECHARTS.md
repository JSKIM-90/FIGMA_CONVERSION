# Figma to ECharts 변환 가이드

## 🎯 개요
Figma의 차트 디자인을 ECharts 옵션으로 변환하는 방법을 설명합니다.

## 🔄 변환 가능한 요소들

### 1. 색상 팔레트
```javascript
// Figma에서 추출한 색상
const figmaColors = {
  primary: '#d9f460',
  critical: '#f43050',
  warning: '#ff9800',
  success: '#4caf50',
  info: '#2196f3'
};

// ECharts 색상 옵션으로 변환
const echartsOption = {
  color: [
    figmaColors.primary,
    figmaColors.critical,
    figmaColors.warning,
    figmaColors.success,
    figmaColors.info
  ]
};
```

### 2. 차트 스타일
```javascript
// Figma 디자인 스타일 → ECharts 옵션
{
  backgroundColor: '#122023',  // Figma 배경색
  textStyle: {
    color: '#cccccc',         // Figma 텍스트 색상
    fontFamily: 'Pretendard',  // Figma 폰트
    fontSize: 14               // Figma 폰트 크기
  },
  grid: {
    left: '64px',             // Figma padding/margin
    right: '64px',
    top: '32px',
    bottom: '32px'
  }
}
```

### 3. 데이터 시각화 매핑

#### Line Chart (선 그래프)
```javascript
// Figma 디자인 속성
const figmaLineStyle = {
  strokeWidth: 2,
  strokeColor: '#d9f460',
  smoothCurve: true,
  showDots: true,
  dotSize: 4
};

// ECharts 변환
{
  series: [{
    type: 'line',
    lineStyle: {
      width: figmaLineStyle.strokeWidth,
      color: figmaLineStyle.strokeColor
    },
    smooth: figmaLineStyle.smoothCurve,
    showSymbol: figmaLineStyle.showDots,
    symbolSize: figmaLineStyle.dotSize
  }]
}
```

#### Bar Chart (막대 그래프)
```javascript
// Figma 막대 디자인
const figmaBarStyle = {
  barWidth: 24,
  barGap: 8,
  borderRadius: [4, 4, 0, 0],
  gradient: {
    start: '#d9f460',
    end: '#a6c132'
  }
};

// ECharts 변환
{
  series: [{
    type: 'bar',
    barWidth: figmaBarStyle.barWidth,
    barGap: `${(figmaBarStyle.barGap / figmaBarStyle.barWidth) * 100}%`,
    itemStyle: {
      borderRadius: figmaBarStyle.borderRadius,
      color: new echarts.graphic.LinearGradient(0, 0, 0, 1, [
        { offset: 0, color: figmaBarStyle.gradient.start },
        { offset: 1, color: figmaBarStyle.gradient.end }
      ])
    }
  }]
}
```

#### Pie Chart (원형 차트)
```javascript
// Figma 원형 차트 스타일
const figmaPieStyle = {
  radius: 100,
  innerRadius: 60,  // 도넛 차트인 경우
  colors: ['#f43050', '#ff9800', '#4caf50', '#2196f3'],
  labelDistance: 20
};

// ECharts 변환
{
  series: [{
    type: 'pie',
    radius: figmaPieStyle.innerRadius ? 
      [`${figmaPieStyle.innerRadius}px`, `${figmaPieStyle.radius}px`] : 
      `${figmaPieStyle.radius}px`,
    color: figmaPieStyle.colors,
    label: {
      distance: figmaPieStyle.labelDistance
    }
  }]
}
```

## 🛠️ 변환 워크플로우

### 1단계: Figma에서 차트 디자인 분석
```javascript
// MCP 도구로 차트 정보 추출
const chartDesign = await mcp.get_code(nodeId);
const designTokens = await mcp.get_variable_defs(nodeId);
const chartImage = await mcp.get_image(nodeId);
```

### 2단계: 디자인 토큰 추출
```javascript
function extractChartTokens(figmaData) {
  return {
    // 색상 시스템
    colors: extractColors(figmaData),
    
    // 간격 시스템
    spacing: extractSpacing(figmaData),
    
    // 타이포그래피
    typography: extractTypography(figmaData),
    
    // 차트별 특성
    chartSpecific: {
      lineWidth: figmaData.strokeWidth,
      barWidth: figmaData.barWidth,
      pieRadius: figmaData.radius
    }
  };
}
```

### 3단계: ECharts 옵션 생성
```javascript
function generateEChartsOption(tokens, chartType, data) {
  const baseOption = {
    // 공통 스타일
    backgroundColor: tokens.colors.background,
    textStyle: {
      fontFamily: tokens.typography.fontFamily,
      fontSize: tokens.typography.fontSize,
      color: tokens.colors.text
    },
    
    // 그리드 설정
    grid: {
      left: tokens.spacing.left,
      right: tokens.spacing.right,
      top: tokens.spacing.top,
      bottom: tokens.spacing.bottom,
      containLabel: true
    },
    
    // 툴팁
    tooltip: {
      trigger: chartType === 'pie' ? 'item' : 'axis',
      backgroundColor: tokens.colors.tooltipBg,
      borderColor: tokens.colors.border,
      textStyle: {
        color: tokens.colors.tooltipText
      }
    },
    
    // 범례
    legend: {
      textStyle: {
        color: tokens.colors.legendText
      },
      icon: 'circle',
      itemWidth: 10,
      itemHeight: 10
    }
  };
  
  // 차트 타입별 시리즈 추가
  switch(chartType) {
    case 'line':
      return { ...baseOption, ...generateLineOption(tokens, data) };
    case 'bar':
      return { ...baseOption, ...generateBarOption(tokens, data) };
    case 'pie':
      return { ...baseOption, ...generatePieOption(tokens, data) };
    default:
      return baseOption;
  }
}
```

## 📊 실제 구현 예시

### Dashboard 이벤트 현황 차트
```javascript
// Figma에서 추출한 이벤트 현황 디자인을 ECharts로
const eventDashboardChart = {
  title: {
    text: '이벤트 현황',
    textStyle: {
      color: '#ffffff',
      fontSize: 20,
      fontWeight: 600
    },
    left: 32,
    top: 16
  },
  
  xAxis: {
    type: 'category',
    data: ['00:00', '04:00', '08:00', '12:00', '16:00', '20:00'],
    axisLine: {
      lineStyle: { color: '#444' }
    },
    axisLabel: {
      color: '#999'
    }
  },
  
  yAxis: {
    type: 'value',
    name: 'Events',
    axisLine: {
      lineStyle: { color: '#444' }
    },
    splitLine: {
      lineStyle: { 
        color: '#333',
        type: 'dashed'
      }
    }
  },
  
  series: [
    {
      name: 'Critical',
      type: 'line',
      smooth: true,
      lineStyle: { color: '#f43050', width: 2 },
      itemStyle: { color: '#f43050' },
      data: [120, 132, 101, 134, 90, 230]
    },
    {
      name: 'Warning',
      type: 'line',
      smooth: true,
      lineStyle: { color: '#ff9800', width: 2 },
      itemStyle: { color: '#ff9800' },
      data: [220, 182, 191, 234, 290, 330]
    },
    {
      name: 'Normal',
      type: 'bar',
      barWidth: 20,
      itemStyle: { 
        color: '#4caf50',
        borderRadius: [2, 2, 0, 0]
      },
      data: [150, 232, 201, 154, 190, 330]
    }
  ]
};
```

## 🎨 고급 변환 기법

### 1. 그라데이션 효과
```javascript
// Figma 그라데이션 → ECharts
itemStyle: {
  color: {
    type: 'linear',
    x: 0, y: 0, x2: 0, y2: 1,
    colorStops: [
      { offset: 0, color: 'rgba(217, 244, 96, 0.8)' },
      { offset: 1, color: 'rgba(217, 244, 96, 0.2)' }
    ]
  }
}
```

### 2. 그림자 효과
```javascript
// Figma 그림자 → ECharts
itemStyle: {
  shadowBlur: 10,
  shadowColor: 'rgba(0, 0, 0, 0.3)',
  shadowOffsetX: 2,
  shadowOffsetY: 2
}
```

### 3. 애니메이션
```javascript
// Figma 인터랙션 → ECharts 애니메이션
animation: true,
animationEasing: 'elasticOut',
animationDuration: 1000,
animationDelay: function(idx) {
  return idx * 50;
}
```

## 💡 활용 시나리오

1. **대시보드 차트 변환**
   - Figma로 디자인한 대시보드 차트를 실제 동작하는 ECharts로 변환
   
2. **디자인 시스템 통합**
   - Figma 디자인 토큰을 ECharts 테마로 자동 변환
   
3. **실시간 데이터 시각화**
   - Figma 디자인 스타일 유지하면서 실시간 데이터 표시

## 📝 주의사항

- Figma는 정적 디자인, ECharts는 동적 차트
- 모든 시각적 효과가 1:1로 변환되지 않을 수 있음
- 인터랙션과 애니메이션은 추가 구현 필요
- 반응형 디자인 고려 필요

## 🔧 트러블슈팅

### 일반적인 문제와 해결

| 문제 | 원인 | 해결 방법 |
|------|------|----------|
| 차트가 렌더링되지 않음 | 컨테이너 크기 미지정 | width, height 명시적 설정 |
| 색상이 Figma와 다름 | 투명도 미적용 | rgba() 형식으로 변환 |
| 폰트가 다르게 보임 | 웹폰트 미로드 | @font-face 또는 CDN 추가 |
| 반응형 동작 안함 | resize 이벤트 미처리 | window.addEventListener('resize', chart.resize) |
| 데이터 업데이트 안됨 | setOption 미호출 | chart.setOption(newOption, true) |

### 성능 최적화 팁

```javascript
// 1. 대량 데이터 처리
const option = {
  dataset: {
    source: largeData  // 데이터셋 사용으로 성능 향상
  },
  dataZoom: [{
    type: 'inside',  // 대량 데이터 줌 기능
    start: 0,
    end: 10
  }]
};

// 2. 애니메이션 최적화
const option = {
  animation: true,
  animationDuration: 500,  // 짧은 애니메이션
  animationEasing: 'linear',  // 간단한 easing
  progressive: 1000,  // 점진적 렌더링
  progressiveThreshold: 2000
};

// 3. 메모리 관리
// 차트 삭제 시 반드시 dispose
chart.dispose();
chart = null;
```

## 🌟 실제 프로젝트 적용 사례

### 대시보드 실시간 차트
```html
<!DOCTYPE html>
<html>
<head>
  <script src="https://cdn.jsdelivr.net/npm/echarts/dist/echarts.min.js"></script>
</head>
<body>
  <div id="dashboard-chart" style="width: 100%; height: 400px;"></div>
  
  <script>
  // Figma 디자인 토큰
  const figmaTokens = {
    colors: {
      primary: '#502EE9',
      success: '#9ECF50',
      warning: '#F9B650'
    }
  };
  
  // 차트 초기화
  const chart = echarts.init(document.getElementById('dashboard-chart'));
  
  // Figma 스타일 적용
  const option = {
    color: Object.values(figmaTokens.colors),
    // ... 옵션 설정
  };
  
  chart.setOption(option);
  
  // 실시간 업데이트
  setInterval(() => {
    // 새 데이터 생성
    const newData = generateRealtimeData();
    
    // 차트 업데이트
    chart.setOption({
      series: [{
        data: newData
      }]
    });
  }, 1000);
  </script>
</body>
</html>
```

## 🚀 자동화 스크립트 예시

```javascript
class FigmaToECharts {
  constructor(mcpServer) {
    this.mcp = mcpServer;
  }
  
  async convert(figmaNodeId) {
    // 1. Figma 데이터 추출
    const design = await this.mcp.get_code(figmaNodeId);
    const tokens = await this.mcp.get_variable_defs(figmaNodeId);
    
    // 2. 차트 타입 판별
    const chartType = this.detectChartType(design);
    
    // 3. ECharts 옵션 생성
    const option = this.generateOption(design, tokens, chartType);
    
    // 4. 차트 렌더링
    return {
      option,
      renderCode: this.generateRenderCode(option)
    };
  }
  
  detectChartType(design) {
    // 디자인 구조 분석하여 차트 타입 판별
    if (design.includes('pie') || design.includes('donut')) return 'pie';
    if (design.includes('bar')) return 'bar';
    if (design.includes('line')) return 'line';
    return 'unknown';
  }
  
  generateOption(design, tokens, type) {
    // 디자인과 토큰을 ECharts 옵션으로 변환
    // ... 구현
  }
  
  generateRenderCode(option) {
    return `
      const chart = echarts.init(document.getElementById('chart'));
      chart.setOption(${JSON.stringify(option, null, 2)});
      window.addEventListener('resize', () => chart.resize());
    `;
  }
}
```