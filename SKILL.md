---
name: data-report-html
description: 生成交互式数据分析HTML报告，包含ECharts图表、KPI卡片、漏斗、表格、洞察卡片等组件，单文件自包含，适合直接发给老板或贴到在线文档
---

# 数据分析 HTML 报告

- **适用场景**：功能分析报告、AB 实验报告、渠道对比分析、用户行为分析、测算/盘点报告
- **已知限制**：单文件 HTML，数据直接嵌入 JS 变量，适合中等数据量（<1MB）；ECharts 依赖 CDN
- **两套组件**：本文件是"洞察型"组件（可折叠背景、洞察卡片、术语表）；`references/design-system.md` 是"叙事型"组件（多 Tab 递进、路线图、差距高亮、预测折线）和 UI 坑点。按报告类型选一套，交付前自检两边都适用


## 设计原则

- **单文件自包含**：除 ECharts CDN 外，CSS/JS/数据全部内联
- **老板友好**：打开即可交互，无需部署，可直接 IM/邮件分享
- **数据驱动**：所有数据集中声明为 JS 变量，图表和 HTML 由数据渲染
- **组件化思维**：用统一组件拼装，不同报告只换数据和组件组合

## 执行流程

### Step 1: 确认报告需求

向用户确认以下信息（未提供的主动询问）：
- **报告主题**：标题、副标题
- **分析维度**：对比维度（如渠道、AB组、平台），每个维度的颜色标签
- **核心指标**：需要展示的KPI列表
- **数据内容**：用户提供数据（表格/SQL结果/口述），或需要现场查询
- **组件选型**：根据数据类型选用合适的组件（见 Step 3）
- **输出路径**：默认 `~/Downloads/{topic}_{date}.html`

### Step 2: 组装 HTML 骨架

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>{报告标题}</title>
<script src="https://cdn.jsdelivr.net/npm/echarts@5.5.1/dist/echarts.min.js"></script>
<style>
/* === 设计系统 === */
:root {
  --bg:#f8f9fb;--card:#fff;--border:#e8eaed;
  --text:#1a1a2e;--text2:#5f6368;--text3:#9aa0a6;
  /* 对比维度颜色：根据实际维度数量定义，下面是 2-4 维示例 */
  --c1:#6366f1;--c2:#f59e0b;--c3:#10b981;--c4:#3b82f6;
  --shadow:0 1px 3px rgba(0,0,0,.08),0 1px 2px rgba(0,0,0,.04);
  --radius:12px;
}
*{margin:0;padding:0;box-sizing:border-box}
body{font-family:-apple-system,BlinkMacSystemFont,"SF Pro Display","Segoe UI",Roboto,sans-serif;background:var(--bg);color:var(--text);line-height:1.6;-webkit-font-smoothing:antialiased}
.container{max-width:1400px;margin:0 auto;padding:24px}

/* Header */
.header{text-align:center;padding:48px 24px 36px;background:linear-gradient(135deg,#667eea 0%,#764ba2 100%);color:#fff;border-radius:0 0 24px 24px;margin-bottom:32px}
.header h1{font-size:28px;font-weight:700;letter-spacing:-.5px}
.header .sub{font-size:15px;opacity:.85;margin-top:8px}
.header .meta{display:flex;gap:24px;justify-content:center;margin-top:16px;font-size:13px;opacity:.8}
.header .meta span{background:rgba(255,255,255,.15);padding:4px 12px;border-radius:20px}

/* Navigation */
.nav{display:flex;gap:8px;margin-bottom:24px;flex-wrap:wrap}
.nav-btn{padding:10px 20px;border:1px solid var(--border);border-radius:8px;background:var(--card);cursor:pointer;font-size:14px;font-weight:500;color:var(--text2);transition:all .2s}
.nav-btn:hover{border-color:#667eea;color:#667eea}
.nav-btn.active{background:#667eea;color:#fff;border-color:#667eea}

/* Cards & Layout */
.card{background:var(--card);border-radius:var(--radius);box-shadow:var(--shadow);padding:24px;margin-bottom:20px}
.card-title{font-size:16px;font-weight:600;margin-bottom:16px;display:flex;align-items:center;gap:8px}
.card-title .dot{width:8px;height:8px;border-radius:50%}
.chart{width:100%;height:350px}
.grid-2{display:grid;grid-template-columns:1fr 1fr;gap:20px}
.grid-3{display:grid;grid-template-columns:1fr 1fr 1fr;gap:20px}
@media(max-width:900px){.grid-2,.grid-3{grid-template-columns:1fr}}

/* Tabs */
.tab-panel{display:none}.tab-panel.active{display:block}
.sub-nav{display:inline-flex;gap:4px;margin-bottom:16px;background:var(--card);padding:4px;border-radius:10px;box-shadow:var(--shadow)}
.sub-nav-btn{padding:8px 20px;border:none;border-radius:8px;background:transparent;color:var(--text2);font-size:14px;font-weight:500;cursor:pointer;transition:all .2s}
.sub-nav-btn:hover{color:var(--text)}
.sub-nav-btn.active{background:#6366f1;color:white;box-shadow:0 2px 8px rgba(99,102,241,.3)}
.sub-panel{display:none}.sub-panel.active{display:block}

/* Legend */
.legend{display:flex;gap:20px;justify-content:center;margin-bottom:16px;font-size:13px}
.legend-item{display:flex;align-items:center;gap:6px}
.legend-dot{width:10px;height:10px;border-radius:50%}

/* Table */
table{width:100%;border-collapse:collapse;font-size:13px}
th,td{padding:10px 12px;text-align:left;border-bottom:1px solid var(--border)}
th{background:#f1f3f5;font-weight:600;color:var(--text2);font-size:12px}
tr:hover td{background:#f8f9fb}

/* Tags */
.tag{display:inline-block;padding:2px 8px;border-radius:4px;font-size:11px;font-weight:600}
/* 按维度定义 tag 颜色，示例: */
.tag-c1{background:#ede9fe;color:#6366f1}
.tag-c2{background:#fef3c7;color:#d97706}
.tag-c3{background:#d1fae5;color:#059669}
.tag-positive{background:#dcfce7;color:#15803d}
.tag-negative{background:#fee2e2;color:#b91c1c}

/* Report Context (可折叠背景说明) */
.report-context{background:var(--card);border-radius:var(--radius);box-shadow:var(--shadow);margin-bottom:24px;overflow:hidden}
.context-toggle{width:100%;padding:16px 24px;display:flex;justify-content:space-between;align-items:center;border:none;background:linear-gradient(135deg,#f0f4ff 0%,#faf5ff 100%);cursor:pointer;font-size:15px;font-weight:600;color:var(--text)}
.context-toggle:hover{background:linear-gradient(135deg,#e8eeff 0%,#f3ecff 100%)}
.context-toggle .arrow{transition:transform .3s;font-size:12px;color:var(--text2)}
.context-toggle .arrow.open{transform:rotate(180deg)}
.context-body{display:none;padding:0 24px 24px}
.context-body.show{display:block}
.context-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(280px,1fr));gap:16px;margin-top:16px}
.context-card{background:var(--bg);border-radius:8px;padding:16px}
.context-card h4{font-size:14px;font-weight:600;margin-bottom:8px;color:#667eea}
.context-card p,.context-card li{font-size:13px;color:var(--text2);line-height:1.7}
.context-card ul{padding-left:18px}

/* Insight Cards (洞察卡片) */
.insight-section{margin-bottom:24px}
.insight-section-header{padding:12px 20px;border-radius:10px;font-size:16px;font-weight:600;color:white;cursor:pointer;display:flex;align-items:center;justify-content:space-between;user-select:none}
.insight-section-header.open{border-radius:10px 10px 0 0}
.insight-section-header .toggle-arrow{transition:transform .2s;font-size:14px}
.insight-section-header.open .toggle-arrow{transform:rotate(180deg)}
.insight-section-body{display:none}.insight-section-body.open{display:grid}
.insight-section-body{gap:12px;padding:16px;background:var(--card);border-radius:0 0 10px 10px;box-shadow:var(--shadow)}
.insight-card{padding:16px;border-radius:8px;background:var(--bg);border-left:4px solid #94a3b8}
.insight-card.level-positive{border-left-color:#22c55e}
.insight-card.level-warning{border-left-color:#f59e0b}
.insight-card.level-critical{border-left-color:#ef4444}
.insight-card.level-neutral{border-left-color:#6366f1}
.insight-card h4{font-size:14px;font-weight:600;margin-bottom:6px}
.insight-card p{font-size:13px;color:var(--text2);line-height:1.7}

/* Glossary Sidebar (术语表侧边栏) */
.glossary-btn{position:fixed;bottom:24px;right:24px;width:48px;height:48px;border-radius:50%;background:linear-gradient(135deg,#667eea,#764ba2);color:white;border:none;cursor:pointer;font-size:20px;box-shadow:0 4px 16px rgba(102,126,234,.4);z-index:999;display:flex;align-items:center;justify-content:center;transition:transform .2s}
.glossary-btn:hover{transform:scale(1.1)}
.glossary-panel{position:fixed;top:0;right:-380px;width:360px;height:100vh;background:var(--card);box-shadow:-4px 0 24px rgba(0,0,0,.12);z-index:1001;transition:right .3s ease;display:flex;flex-direction:column}
.glossary-panel.open{right:0}
.glossary-header{padding:20px 24px 12px;border-bottom:1px solid var(--border)}
.glossary-header h3{font-size:16px;font-weight:600;margin-bottom:8px}
.glossary-search{width:100%;padding:8px 12px;border:1px solid var(--border);border-radius:8px;font-size:13px;outline:none}
.glossary-search:focus{border-color:#667eea}
.glossary-list{flex:1;overflow-y:auto;padding:12px 24px}
.glossary-item{padding:12px 0;border-bottom:1px solid var(--border)}
.glossary-item:last-child{border:none}
.glossary-item .term{font-size:14px;font-weight:600;margin-bottom:4px}
.glossary-item .def{font-size:13px;color:var(--text2);line-height:1.6}
.glossary-overlay{display:none;position:fixed;inset:0;background:rgba(0,0,0,.3);z-index:1000}
.glossary-overlay.show{display:block}
.glossary-close{position:absolute;top:16px;right:16px;width:28px;height:28px;border:none;background:var(--bg);border-radius:6px;cursor:pointer;font-size:16px;display:flex;align-items:center;justify-content:center}

/* Term Tooltip (行内术语悬浮提示) */
.term-tip{position:relative;border-bottom:1px dashed var(--text3);cursor:help}
.term-tip:hover::after{content:attr(data-tip);position:absolute;top:100%;left:50%;transform:translateX(-50%);margin-top:6px;background:#1a1a2e;color:white;padding:8px 12px;border-radius:6px;font-size:12px;white-space:normal;width:max-content;max-width:280px;line-height:1.5;z-index:1100;pointer-events:none}
</style>
</head>
```

### Step 3: 按需组装组件

根据数据类型选择组件，用以下模板拼装到 `<body>` 中。每个组件都是独立 HTML 片段。

---

#### 3.1 Header（必选）

```html
<div class="header">
  <h1>{报告标题}</h1>
  <div class="sub">{副标题/简要说明}</div>
  <div class="meta">
    <span>{日期范围}</span>
    <span>{数据范围说明}</span>
    <span>{版本/口径}</span>
  </div>
</div>
```

#### 3.2 报告背景（推荐）

可折叠的背景说明区域，放分析目标、数据概览、数据来源、归因规则等。

```html
<div class="report-context">
  <button class="context-toggle" onclick="toggleContext()">
    <span>&#128218; 报告背景与研究说明</span>
    <span class="arrow" id="context-arrow">&#9660;</span>
  </button>
  <div class="context-body" id="context-body">
    <div class="context-grid">
      <div class="context-card">
        <h4>{分类标题}</h4>
        <p>{内容}</p>
      </div>
      <!-- 重复 context-card -->
    </div>
  </div>
</div>
```

#### 3.3 KPI 指标卡（核心数据概览）

每个维度一个卡片，卡片顶部有维度色条。内部用 2 列 grid 排列指标。

```html
<div class="grid-3">
  <div class="card" style="border-top:3px solid var(--c1)">
    <div class="card-title"><div class="dot" style="background:var(--c1)"></div>{维度名}</div>
    <div style="display:grid;grid-template-columns:1fr 1fr;gap:12px;font-size:13px;">
      <div><span style="color:var(--text3)">{指标名}</span><br><b style="font-size:20px">{值}</b></div>
      <!-- 重复指标项 -->
    </div>
  </div>
  <!-- 重复每个维度 -->
</div>
```

#### 3.4 大数字卡片（单一关键指标突出展示）

```html
<div class="card" style="border-top:3px solid var(--c1)">
  <div class="card-title"><div class="dot" style="background:var(--c1)"></div>{标题}</div>
  <div style="text-align:center">
    <div style="font-size:36px;font-weight:700;color:var(--c1)">{数值}</div>
    <div style="font-size:12px;color:var(--text3)">{说明}</div>
  </div>
</div>
```

#### 3.5 ECharts 折线图（趋势对比）

HTML 占位：
```html
<div class="card">
  <div class="card-title">{图表标题}</div>
  <div id="{chart_id}" class="chart"></div>
</div>
```

JS 使用辅助函数 `lc()`：
```javascript
lc('{chart_id}', dates, [
  {name:"{维度1}", type:"line", data:[...], smooth:true, symbol:"circle", symbolSize:6,
   itemStyle:{color:"var(--c1)"}, lineStyle:{width:2.5}},
  // 每个维度一条线
], '{value}%'); // 可选 Y 轴格式化
```

#### 3.6 ECharts 柱状图 + 折线叠加（行为 × 转化率）

```javascript
(function(){
  var c=echarts.init(document.getElementById('{chart_id}'));
  c.setOption({
    tooltip:{trigger:'axis',axisPointer:{type:'cross'}},
    legend:{bottom:0,textStyle:{fontSize:11}},
    grid:{left:50,right:50,top:20,bottom:40},
    xAxis:{type:'category',data:["{分组1}","{分组2}",...],axisLabel:{fontSize:11}},
    yAxis:[
      {type:'value',name:'{左轴名}',position:'left',axisLabel:{fontSize:10}},
      {type:'value',name:'{右轴名}',position:'right',max:100,axisLabel:{fontSize:10,formatter:'{value}%'}}
    ],
    series:[
      {name:'{左轴}',type:'bar',data:[...],itemStyle:{color:'var(--c1)',borderRadius:[4,4,0,0]},barWidth:'40%',
        label:{show:true,position:'top',fontSize:10,color:'#666'}},
      {name:'{右轴}',type:'line',yAxisIndex:1,data:[...],symbol:'circle',symbolSize:8,
        lineStyle:{width:2.5,color:'#ef4444'},itemStyle:{color:'#ef4444'},
        label:{show:true,position:'top',fontSize:10,formatter:'{c}%',color:'#ef4444'}}
    ]
  });
  _charts.push(c);
})();
```

#### 3.7 ECharts 水平柱状图（排名/场景对比）

```javascript
(function(){
  var c=echarts.init(document.getElementById('{chart_id}'));
  var d={pages:[...], values1:[...], values2:[...]};
  c.setOption({
    tooltip:{trigger:'axis'},
    grid:{left:120,right:20,top:10,bottom:20},
    yAxis:{type:'category',data:d.pages.slice().reverse(),axisLabel:{fontSize:10}},
    xAxis:{type:'value'},
    series:[
      {name:'{系列1}',type:'bar',data:d.values1.slice().reverse(),itemStyle:{color:'#94a3b8'},barWidth:'30%'},
      {name:'{系列2}',type:'bar',data:d.values2.slice().reverse(),itemStyle:{color:'var(--c1)'},barWidth:'30%'}
    ]
  });
  _charts.push(c);
})();
```

#### 3.8 CSS 漏斗图（转化漏斗）

纯 CSS 实现，不依赖 ECharts。每一层是一个横向 bar，宽度按比例缩放，标注流失率。

```html
<div style="display:flex;align-items:center;gap:10px">
  <div style="width:70px;text-align:right;font-size:12px;font-weight:500;flex-shrink:0">{步骤名}</div>
  <div style="flex:1;display:flex;align-items:center;gap:8px">
    <div style="width:{pct}%;background:{color};border-radius:6px;padding:8px 14px;text-align:center;font-size:13px;font-weight:600;color:{textColor}">
      {百分比}%<div style="font-size:11px;font-weight:400;opacity:.7">{绝对值}</div>
    </div>
    <span style="color:#ef4444;font-size:12px;white-space:nowrap">&#8595; 流失{drop}%</span>
  </div>
</div>
<div style="text-align:center;color:#cbd5e1;font-size:14px;line-height:1">&#8595;</div>
```

推荐颜色序列（从上到下）：`#e2e8f0, #c4b5fd, #93c5fd, #86efac, #fde68a, #fca5a5`
对应文字色：`#475569, #6d28d9, #1d4ed8, #047857, #92400e, #b91c1c`

**动态漏斗**（支持日期切换）：将数据放入 JS 变量，用 `switchFunnel()` 函数渲染：

```javascript
var _funnelData = {dim1: {all:[...], daily:{yyyymmdd:[...]}}, dim2:{...}};
var _funnelLabels = ["{步骤1}","{步骤2}",...];
// 渲染函数模式同参考HTML
```

#### 3.9 数据表格

```html
<div class="card">
  <div class="card-title"><div class="dot" style="background:var(--c1)"></div>{标题}</div>
  <table>
    <tr><th>{列1}</th><th>{列2}</th>...</tr>
    <tr><td>{值}</td><td>{值}</td>...</tr>
  </table>
</div>
```

#### 3.10 洞察卡片（分析结论）

数据定义：
```javascript
var insightsData = {
  "{分类1}": [
    {level:"positive|warning|critical|neutral", title:"{标题}", detail:"{详细说明}"},
    ...
  ],
  ...
};
```

HTML 渲染：由 JS 自动渲染到 `<div id="insights-container"></div>`，支持折叠。每个分类一个彩色 header，下方为洞察卡片网格。

渲染代码：
```javascript
(function(){
  var container=document.getElementById('insights-container');
  var sectionOrder=['{分类1}','{分类2}',...];
  var sectionCls={'{分类1}':'c1','{分类2}':'c2',...};
  container.innerHTML=sectionOrder.map(function(section){
    var items=insightsData[section]||[];
    return '<div class="insight-section">'
      +'<div class="insight-section-header '+sectionCls[section]+'" onclick="toggleInsight(this)">'
      +'<span>'+section+'<span style="font-size:13px;font-weight:400;opacity:0.85;margin-left:8px">'+items.length+'条洞察</span></span>'
      +'<span class="toggle-arrow">&#9660;</span>'
      +'</div><div class="insight-section-body">'
      +items.map(function(ins){
        return '<div class="insight-card level-'+(ins.level||'neutral')+'">'
          +'<h4>'+ins.title+'</h4><p>'+ins.detail+'</p></div>';
      }).join('')
      +'</div></div>';
  }).join('');
})();
```

#### 3.11 术语表侧边栏（推荐）

在报告中涉及专业术语时添加。右下角浮动按钮触发。

数据：
```javascript
var glossaryData = {
  "UTR": "点击到播放的转化率 = 播放UV / 点击UV",
  "VV": "Video View，视频播放次数",
  ...
};
```

行内使用 tooltip：
```html
<span class="term-tip" data-tip="点击到播放的转化率 = 播放UV / 点击UV">UTR</span>
```

### Step 4: 组装 JS 基础设施

在 `</body>` 前、所有数据和图表代码之前，放入以下基础 JS：

```javascript
<script>
// 全局图表数组（用于 resize 和 tab 切换时刷新）
var _charts=[];

// Tab 切换
function showTab(i){
  document.querySelectorAll('.tab-panel').forEach(function(p){p.classList.remove('active')});
  document.querySelectorAll('.nav-btn').forEach(function(b){b.classList.remove('active')});
  document.getElementById('tab'+i).classList.add('active');
  document.querySelectorAll('.nav-btn')[i].classList.add('active');
  setTimeout(function(){_charts.forEach(function(c){c.resize()})},50);
}

// Sub-tab 切换
function showSubTab(tabIdx,subIdx,btn){
  var tab=document.getElementById('tab'+tabIdx);
  tab.querySelectorAll('.sub-panel').forEach(function(p){p.classList.remove('active')});
  tab.querySelectorAll('.sub-nav-btn').forEach(function(b){b.classList.remove('active')});
  document.getElementById('tab'+tabIdx+'_'+subIdx).classList.add('active');
  btn.classList.add('active');
  setTimeout(function(){_charts.forEach(function(c){c.resize()})},50);
}

// 折线图快捷函数
function lc(el,xd,ss,yf){
  var c=echarts.init(document.getElementById(el));
  _charts.push(c);
  c.setOption({
    tooltip:{trigger:'axis'},
    legend:{bottom:0,textStyle:{fontSize:11}},
    grid:{left:60,right:20,top:20,bottom:40},
    xAxis:{type:'category',data:xd,axisLabel:{fontSize:11}},
    yAxis:{type:'value',axisLabel:{fontSize:11,formatter:yf||'{value}'}},
    series:ss
  });
}

// 响应式
window.addEventListener('resize',function(){_charts.forEach(function(c){c.resize()})});

// 折叠：报告背景
function toggleContext(){
  var b=document.getElementById('context-body');
  var a=document.getElementById('context-arrow');
  b.classList.toggle('show');
  a.classList.toggle('open');
}

// 折叠：洞察分类
function toggleInsight(header){
  header.classList.toggle('open');
  header.nextElementSibling.classList.toggle('open');
}

// 术语表
function toggleGlossary(){
  document.getElementById('glossary-panel').classList.toggle('open');
  document.getElementById('glossary-overlay').classList.toggle('show');
}
function filterGlossary(q){
  var query=q.toLowerCase();
  document.querySelectorAll('.glossary-item').forEach(function(item){
    item.style.display=(item.dataset.term.includes(query)||item.textContent.toLowerCase().includes(query))?'':'none';
  });
}
</script>
```

### Step 5: 填充数据 & 生成

1. 将用户提供的数据转为 JS 变量（`var dates=[...]`, `var metricsData={...}` 等）
2. 用 `lc()` 快捷函数批量生成折线图
3. 用 IIFE 生成复杂图表（柱线叠加、横向柱状图）
4. 漏斗数据用结构化 JSON + 渲染函数
5. 洞察数据用分类 JSON + 自动渲染

### Step 6: 写入文件并验证

1. 用 Write 工具一次性写入完整 HTML
2. 用 `open {filepath}` 在浏览器中打开验证
3. 检查项：
   - 所有 Tab 可切换
   - 图表正常渲染（无空白）
   - 漏斗百分比和流失率计算正确
   - 响应式：缩小窗口后 grid 降为单列
   - 洞察卡片可折叠展开

## 组件选型指南

| 数据类型 | 推荐组件 | 何时使用 |
|---------|---------|---------|
| 3-8个核心指标 | KPI 指标卡 (3.3) | 每个维度的数据概览 |
| 单一关键数字 | 大数字卡片 (3.4) | 突出展示占比/指数/分数 |
| 日趋势、多维对比 | 折线图 (3.5) | 指标随时间变化 |
| 分组 × 转化率 | 柱线叠加图 (3.6) | 行为分桶 × 留存率 |
| Top N 排名 | 水平柱状图 (3.7) 或表格 (3.9) | 场景/内容/关键词排名 |
| 转化链路 | CSS 漏斗 (3.8) | 从曝光到转化的逐步流失 |
| 明细数据 | 表格 (3.9) | 热门内容、搜索词、用户列表 |
| 分析结论 | 洞察卡片 (3.10) | 报告最后的 insight 汇总 |
| 专业术语 | 术语表 (3.11) + tooltip | 报告含非通用术语时 |

## 颜色方案

默认提供的维度色板（最多 6 维）：

| 序号 | 色值 | 用途 |
|-----|------|------|
| c1 | #6366f1 (Indigo) | 第一维度 |
| c2 | #f59e0b (Amber) | 第二维度 |
| c3 | #10b981 (Emerald) | 第三维度 |
| c4 | #3b82f6 (Blue) | 第四维度 |
| c5 | #ec4899 (Pink) | 第五维度 |
| c6 | #8b5cf6 (Violet) | 第六维度 |

Insight 级别色：positive=#22c55e, warning=#f59e0b, critical=#ef4444, neutral=#6366f1

## 质量检查清单

- [ ] 标题、日期、口径说明完整
- [ ] 所有数值与原始数据一致（特别注意百分比计算）
- [ ] ECharts 图表 ID 无重复
- [ ] 所有图表都 push 到 `_charts` 数组（确保 resize 生效）
- [ ] 漏斗流失率 = (上一步 - 当前步) / 上一步 × 100
- [ ] Tab 数量与 nav-btn 数量一致
- [ ] 移动端 grid 降级为单列
- [ ] 洞察卡片 level 使用正确（positive/warning/critical/neutral）

## 已知坑点

- ECharts Y 轴 formatter 里的 `{value}` 不能用 ES6 模板字符串，必须用普通字符串 `'{value}%'`
- 漏斗宽度百分比要设最小值（如 12%），防止数据太小时 bar 无法显示文字
- `_charts` 数组必须在所有图表初始化之前声明
- Tab 切换后图表需要 `resize()`，用 `setTimeout 50ms` 等 DOM 更新
- 多个 IIFE 图表如果用相同变量名（如 `c`, `d`），每个必须包在自己的 IIFE 里
