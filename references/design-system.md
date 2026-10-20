# HTML 报告设计系统（叙事型）

纯排版/字体/组件/图表样式，**不含任何业务口径**。数据报告类交付物统一用这套设计系统，保证跨报告视觉一致。

---

## 骨架

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>{标题}</title>
<script src="https://cdn.jsdelivr.net/npm/echarts@5.5.1/dist/echarts.min.js"></script>
<style>
/* === 完整CSS === */
</style>
</head>
<body>
<div class="header">...</div>
<div class="container">
  <div class="nav">...</div>
  <div class="tab-panel active" id="tab0">...</div>
  <div class="tab-panel" id="tab1">...</div>
  <div class="tab-panel" id="tab2">...</div>
</div>
<script>
/* === 数据区 + 图表init，必须是独立 script 块（无 src 属性）=== */
</script>
</body>
</html>
```

Tab 数量按叙事需要定，不固定三个。单主题报告可以只一个 Tab（此时省掉 `.nav`）。

---

## 完整 CSS（整段复制）

```css
:root{
  --bg:#f8f9fb;--card:#fff;--border:#e8eaed;
  --text:#1a1a2e;--text2:#5f6368;--text3:#9aa0a6;
  --c1:#6366f1;--c2:#f59e0b;--c3:#10b981;--c4:#3b82f6;--c5:#ec4899;
  --shadow:0 1px 3px rgba(0,0,0,.08),0 1px 2px rgba(0,0,0,.04);
  --radius:12px;
}
*{margin:0;padding:0;box-sizing:border-box}
body{font-family:-apple-system,BlinkMacSystemFont,"SF Pro Display","Segoe UI",Roboto,sans-serif;background:var(--bg);color:var(--text);line-height:1.6;-webkit-font-smoothing:antialiased}
.container{max-width:1280px;margin:0 auto;padding:24px}

.header{text-align:center;padding:40px 24px 32px;background:linear-gradient(135deg,#667eea 0%,#764ba2 100%);color:#fff;border-radius:0 0 24px 24px;margin-bottom:28px}
.header h1{font-size:26px;font-weight:700;letter-spacing:-.5px}
.header .sub{font-size:14px;opacity:.85;margin-top:6px}
.header .meta{display:flex;gap:20px;justify-content:center;margin-top:14px;font-size:12px;opacity:.75;flex-wrap:wrap}

.nav{display:flex;gap:6px;margin-bottom:22px;flex-wrap:wrap}
.nav-btn{padding:9px 18px;border:1px solid var(--border);border-radius:8px;background:var(--card);cursor:pointer;font-size:13px;font-weight:500;color:var(--text2);transition:all .2s}
.nav-btn:hover{border-color:#667eea;color:#667eea}
.nav-btn.active{background:#667eea;color:#fff;border-color:#667eea}

.tab-panel{display:none}.tab-panel.active{display:block}

.card{background:var(--card);border-radius:var(--radius);box-shadow:var(--shadow);padding:22px;margin-bottom:18px}
.card-title{font-size:15px;font-weight:600;margin-bottom:14px;display:flex;align-items:center;gap:8px}
.card-title .dot{width:8px;height:8px;border-radius:50%;flex-shrink:0}
.chart{width:100%;height:380px}

.grid-2{display:grid;grid-template-columns:1fr 1fr;gap:18px}
.grid-3{display:grid;grid-template-columns:repeat(3,1fr);gap:18px}
@media(max-width:900px){.grid-2,.grid-3{grid-template-columns:1fr}}

.insight-box{background:#ede9fe;border-left:4px solid #6366f1;padding:14px 18px;border-radius:8px;margin-bottom:12px;font-size:13px;line-height:1.8}
.insight-box.warn{background:#fef3c7;border-left-color:#f59e0b}
.insight-box.good{background:#d1fae5;border-left-color:#10b981}

.kpi-row{display:flex;gap:14px;margin-bottom:18px;flex-wrap:wrap}
.kpi-card{flex:1;min-width:180px;background:var(--card);border-radius:var(--radius);box-shadow:var(--shadow);padding:18px;text-align:center;border-top:3px solid var(--c1)}
.kpi-card .kv{font-size:26px;font-weight:700}
.kpi-card .kl{font-size:11px;color:var(--text2);margin-top:3px}
.kpi-card .ks{font-size:10px;color:var(--text3);margin-top:1px}

.funnel-row{display:flex;gap:20px;flex-wrap:wrap;justify-content:center;align-items:flex-start}
.funnel-box{flex:1;min-width:280px;max-width:420px;display:flex;flex-direction:column;align-items:center}
.funnel-box h4{font-size:13px;font-weight:600;text-align:center;margin-bottom:10px;color:var(--text2);white-space:nowrap}
.funnel-layer{display:flex;align-items:center;gap:8px;width:100%;justify-content:center;margin-bottom:2px}
.funnel-label{width:80px;text-align:right;font-size:11px;font-weight:600;color:var(--text2);flex-shrink:0}
.funnel-bar{display:flex;align-items:center;justify-content:center;padding:12px 0;border-radius:7px;font-size:13px;font-weight:700;color:#fff;text-align:center;min-width:100px;flex-shrink:0}
.funnel-info{font-size:10px;font-weight:400;opacity:.8;display:block}
.funnel-arrow{text-align:center;color:var(--text3);font-size:14px;line-height:1;padding:3px 0}
.funnel-drop{width:60px;font-size:11px;font-weight:600;text-align:left;flex-shrink:0;color:#6366f1}

.strategy-roadmap{display:flex;gap:0;align-items:stretch;margin-bottom:24px}
.road-step{flex:1;display:flex;flex-direction:column}
.road-connector{display:flex;align-items:center;justify-content:center;width:32px;flex-shrink:0;padding-top:28px}
.road-connector svg{width:24px;height:24px}
.road-connector path{stroke:#c4b5fd;stroke-width:2;fill:none}
.road-head{text-align:center;padding:20px 12px 14px;border-radius:12px 12px 0 0;color:#fff;position:relative}
.road-head .rh-num{font-size:28px;font-weight:800;opacity:.35;position:absolute;top:6px;left:12px;line-height:1}
.road-head .rh-title{font-size:15px;font-weight:700;margin-bottom:3px}
.road-head .rh-sub{font-size:11px;opacity:.85}
.road-body{background:var(--card);border-radius:0 0 12px 12px;padding:16px 14px;flex:1;border:1px solid var(--border);border-top:none;box-shadow:var(--shadow)}
.road-body .rb-item{margin-bottom:10px;font-size:12px;line-height:1.65}
.road-body .rb-item:last-child{margin-bottom:0}
.road-body .rb-label{font-size:11px;font-weight:600;color:var(--text2);margin-bottom:2px}
.road-body .rb-text{color:var(--text2);font-size:12px}
.road-time{display:inline-block;font-size:9px;padding:2px 7px;border-radius:8px;font-weight:600;margin-top:4px;background:rgba(255,255,255,.3);color:#fff}
@media(max-width:1000px){.strategy-roadmap{flex-direction:column}.road-connector{width:100%;padding:8px 0;transform:rotate(90deg)}}

.table-scroll{overflow-x:auto;max-height:520px;overflow-y:auto}
table{width:100%;border-collapse:collapse;font-size:12px}
th,td{padding:8px 10px;text-align:center;border-bottom:1px solid #e8eaed}
th{background:#f1f3f5;font-weight:600;color:#5f6368;font-size:11px;white-space:nowrap}
tr:hover td{background:#f8f9fb}
td:first-child,th:first-child{text-align:left}
.note{font-size:11px;color:var(--text3);margin-top:10px;line-height:1.6}

.gap-highlight{padding:16px;border-radius:10px;margin-bottom:14px;text-align:center}
.gap-highlight .gap-num{font-size:28px;font-weight:700}
.gap-highlight .gap-label{font-size:12px;color:var(--text2);margin-top:2px}
```

---

## JS 框架

```js
var _charts=[];

function showTab(i,btn){
  document.querySelectorAll('.tab-panel').forEach(function(p){p.classList.remove('active')});
  document.querySelectorAll('.nav-btn').forEach(function(b){b.classList.remove('active')});
  document.getElementById('tab'+i).classList.add('active');btn.classList.add('active');
  setTimeout(function(){_charts.forEach(function(c){try{c.resize()}catch(e){}})},200);
}
window.addEventListener('resize',function(){_charts.forEach(function(c){try{c.resize()}catch(e){}})});

// ═══ 数据区 —— 从落盘的 json/csv 转来，不手写 ═══
var days=[...];      // x轴标签
var cats=[...];      // 分类维度标签

// ═══ 图表区 —— 每个图表 IIFE 包裹，init 后 push 到 _charts ═══
```

**数据区原则**：数字来自落盘的 `forecast.json` / CSV，用脚本生成 JS 数组，不在对话里手抄。避免"文档里的数字和 HTML 里的数字不一致"。

---

## 颜色语义

| 变量 | 色值 | 语义 |
|---|---|---|
| c1 | #6366f1 靛蓝 | 主色，主体数据/趋势/主推方案 |
| c2 | #f59e0b 琥珀 | 警告/差距/待决策 |
| c3 | #10b981 翠绿 | 正向/达标/已完成 |
| c4 | #3b82f6 蓝 | 辅助数据维度 |
| c5 | #ec4899 粉 | 特殊标注/对比 |
| text3 | #9aa0a6 灰 | 对照组/基线/次要项 |

对比图里"我们的东西"用 c1，"对照/大盘/基线"用灰或浅色（`#c7d2fe`），别两边都用饱和色。

---

## 通用组件

### Header

```html
<div class="header">
  <h1>{标题}</h1>
  <div class="sub">{副标题}</div>
  <div class="meta"><span>{数据窗口}</span><span>{口径}</span><span>{生成日期}</span></div>
</div>
```

`meta` 放数据窗口、口径、生成时间——读者第一眼要能判断这份报告是什么时候什么口径的。

### Nav Tab

```html
<div class="nav">
  <button class="nav-btn active" onclick="showTab(0,this)">{Tab0}</button>
  <button class="nav-btn" onclick="showTab(1,this)">{Tab1}</button>
</div>
```

### insight-box（结论条）

```html
<div class="insight-box"><b>标题</b>：内容</div>
<div class="insight-box warn"><b>标题</b>：内容</div>
<div class="insight-box good"><b>标题</b>：内容</div>
```

内嵌多列（顶部总结用）：

```html
<div class="insight-box">
  <b>关键判断</b>
  <div style="display:grid;grid-template-columns:1fr 1fr 1fr;gap:16px;margin-top:10px">
    <div>
      <div style="font-weight:700;color:#10b981;font-size:13px;margin-bottom:2px">{项} · 达标</div>
      <div style="font-size:12px;color:var(--text2)">{一句话}</div>
    </div>
    <!-- 更多列 -->
  </div>
</div>
```

### KPI 行

```html
<div class="kpi-row">
  <div class="kpi-card" style="border-top-color:#6366f1">
    <div class="kv" style="color:#6366f1">128.6万</div>
    <div class="kl">{指标名}</div>
    <div class="ks">{口径/时点}</div>
  </div>
  <!-- 更多卡 -->
</div>
```

`ks` 一定要写口径或时点，裸数字会被误读。

### card

```html
<div class="card">
  <div class="card-title"><div class="dot" style="background:{color}"></div>{标题}</div>
  <!-- 内容或 <div class="chart" id="ch-xxx"></div> -->
</div>
```

dot 色按语义：靛蓝=数据 / 翠绿=正向 / 琥珀=差距 / 粉=对比。

### 网格

```html
<div class="grid-2"><!-- 两栏 --></div>
<div class="grid-3"><!-- 三栏 --></div>
```

### 章节分隔

```html
<div style="border-top:2px solid var(--border);margin:20px 0 16px;padding-top:16px">
  <div style="font-size:16px;font-weight:700;color:var(--text)">{章节标题}</div>
</div>
```

### 漏斗

HTML 占位 `<div id="funnel-x"></div>`，JS 渲染（层数自定）：

```js
function bf(id,layers,rates){
  // layers: [{label,count,pct,color}, ...]  rates: [null, 转化率1, 转化率2, ...]
  var maxW=330,h='';
  for(var i=0;i<layers.length;i++){
    var l=layers[i],w=Math.max(l.pct/100*maxW,80);
    h+='<div class="funnel-layer"><div class="funnel-label">'+l.label+'</div>'
      +'<div class="funnel-bar" style="width:'+w+'px;background:'+l.color+'">'+l.count
      +'<span class="funnel-info">'+l.pct.toFixed(1)+'%</span></div>'
      +'<div class="funnel-drop">'+(i>0?'→ '+rates[i].toFixed(1)+'%':'')+'</div></div>';
    if(i<layers.length-1)h+='<div class="funnel-arrow">↓</div>';
  }
  document.getElementById(id).innerHTML=h;
}
```

条宽按占比缩放但设 80px 下限，否则末层窄到读不出数字。

### 对比卡（A vs B）

```html
<div class="card">
  <div class="card-title"><div class="dot" style="background:#f59e0b"></div>{A} vs {B}</div>
  <div style="display:flex;gap:24px;justify-content:center;align-items:center;padding-top:40px">
    <div style="text-align:center">
      <div style="font-size:11px;color:var(--text2);margin-bottom:6px">{标签A}</div>
      <div style="font-size:32px;font-weight:700;color:#6366f1">{值A}</div>
      <div style="font-size:11px;color:var(--text3);margin-top:4px">{说明}</div>
    </div>
    <div style="font-size:28px;color:var(--text3)">vs</div>
    <div style="text-align:center">
      <div style="font-size:11px;color:var(--text2);margin-bottom:6px">{标签B}</div>
      <div style="font-size:32px;font-weight:700;color:#9aa0a6">{值B}</div>
      <div style="font-size:11px;color:var(--text3);margin-top:4px">{说明}</div>
    </div>
  </div>
  <div style="margin-top:24px;text-align:center;font-size:12px;color:var(--text2)">{结论}</div>
</div>
```

### 差距高亮

```html
<div class="gap-highlight" style="background:#fef3c7">
  <div class="gap-num" style="color:#d97706">-12.0万</div>
  <div class="gap-label">{差距说明}</div>
</div>
```

### 路线图（阶段推进）

```html
<div class="strategy-roadmap">
  <div class="road-step">
    <div class="road-head" style="background:#6366f1">
      <div class="rh-num">1</div>
      <div class="rh-title">{标题}</div>
      <div class="rh-sub">{副标题}</div>
      <div class="road-time">{状态}</div>
    </div>
    <div class="road-body">
      <div class="rb-item"><div class="rb-label">{标签}</div><div class="rb-text">{内容}</div></div>
    </div>
  </div>
  <div class="road-connector">
    <svg viewBox="0 0 24 24"><path d="M5 12h13M16 7l5 5-5 5" stroke-linecap="round" stroke-linejoin="round"/></svg>
  </div>
  <!-- 更多步骤 -->
</div>
```

每个 `road-head` 都要有 `road-time`，否则卡片高度不齐。

### 状态卡（road-body 内嵌）

```html
<div style="border-left:3px solid {border}; padding:10px 14px; margin-bottom:10px; border-radius:6px; background:{bg}">
  <div style="font-size:13px;font-weight:600;color:{text};margin-bottom:4px">{标题} · {状态}</div>
  <div style="font-size:12px;color:var(--text2);line-height:1.7">{描述}</div>
</div>
```

| 状态 | border-left | background | color |
|---|---|---|---|
| 进行中 | #6366f1 | #f5f3ff | #6366f1 |
| 已完成 | #10b981 | #ecfdf5 | #059669 |
| 待决策 | #f59e0b | #fffbeb | #d97706 |
| 暂不启动 | #9aa0a6 | #f8f9fb | #9aa0a6 |

最后一张去掉 `margin-bottom`。

### 矩阵表（行=维度，列=对比项）

```html
<div class="card">
  <div class="card-title"><div class="dot" style="background:#6366f1"></div>{标题}</div>
  <table>
    <tr><th></th><th>{列A}</th><th>{列B}</th><th>{倍数/差}</th></tr>
    <tr><td style="font-weight:600">{行名}</td>
        <td style="font-weight:700;color:#6366f1">{值}</td>
        <td style="color:var(--text2)">{值}</td>
        <td style="color:#f59e0b;font-weight:600">{比}</td></tr>
    <tr style="background:#fef3c7"><td style="font-weight:600">{结论行}</td>
        <td colspan="2" style="font-weight:700;color:#f59e0b;font-size:16px">{值}</td><td></td></tr>
  </table>
  <div style="margin-top:12px;font-size:12px;color:var(--text2);text-align:center">{结论}</div>
</div>
```

结论行用 `background:#fef3c7` 高亮。多行长表包一层 `<div class="table-scroll">`。

---

## ECharts 模板

初始化骨架（每图独立 IIFE）：

```js
(function(){
  var c=echarts.init(document.getElementById('ch-xxx'));_charts.push(c);
  c.setOption({
    tooltip:{trigger:'axis'},
    legend:{bottom:0},
    grid:{left:55,right:20,top:20,bottom:40},
    xAxis:{type:'category',data:days},
    yAxis:{type:'value'},
    series:[...]
  });
})();
```

### 分组柱状图（两组对比）

```js
xAxis:{type:'category',data:cats},
yAxis:{type:'value',axisLabel:{formatter:'{value}%'}},
series:[
  {name:'{主体}',type:'bar',data:[...],barWidth:'40%',
   itemStyle:{color:'#6366f1',borderRadius:[4,4,0,0]},
   label:{show:true,position:'top',fontSize:12,formatter:'{c}%',fontWeight:600}},
  {name:'{对照}',type:'bar',data:[...],barWidth:'40%',
   itemStyle:{color:'#c7d2fe',borderRadius:[4,4,0,0]},
   label:{show:true,position:'top',fontSize:12,formatter:'{c}%',fontWeight:400}}
]
```

### 折线图（多组对比，symbol 区分）

```js
grid:{left:55,right:12,top:16,bottom:40},
xAxis:{type:'category',data:days,axisLabel:{fontSize:10}},
legend:{bottom:0,itemWidth:10,itemHeight:8,textStyle:{fontSize:10}},
series:[
  {name:'{主体}',type:'line',data:[...],smooth:true,symbol:'circle',symbolSize:5,
   lineStyle:{width:2.5,color:'#6366f1'},itemStyle:{color:'#6366f1'},
   label:{show:true,fontSize:8,formatter:'{c}'}},
  {name:'{对照}',type:'line',data:[...],smooth:true,symbol:'diamond',symbolSize:5,
   lineStyle:{width:2,color:'#9aa0a6',type:'dashed'},itemStyle:{color:'#9aa0a6'}}
]
// symbol 固定映射：circle=主体 / diamond=对照 / triangle=第三系列
```

### 折线图 + 目标线

```js
yAxis:{type:'value',axisLabel:{formatter:'{value}%'},min:0},
series:[{type:'line',data:[...],smooth:true,symbol:'circle',symbolSize:8,
  lineStyle:{width:3,color:'#f59e0b'},itemStyle:{color:'#f59e0b'},
  areaStyle:{color:'rgba(245,158,11,.1)'},
  label:{show:true,fontSize:10,formatter:'{c}%',fontWeight:600},
  markLine:{silent:true,data:[{yAxis:{目标值},label:{formatter:'目标 {值}',fontSize:10},
   lineStyle:{color:'#10b981',type:'dashed',width:2}}]}
}]
```

### 预测折线（实测 + 预测分段）

实测段实线、预测段虚线，用两个 series，预测段前面补 `null` 对齐 x 轴：

```js
series:[
  {name:'实测',type:'line',data:actual,smooth:true,symbol:'circle',symbolSize:5,
   lineStyle:{width:2.5,color:'#6366f1'},itemStyle:{color:'#6366f1'}},
  {name:'预测',type:'line',data:forecast,smooth:true,symbol:'none',
   lineStyle:{width:2,color:'#6366f1',type:'dashed'},itemStyle:{color:'#6366f1'}}
]
// forecast = 实测段长度个 null + 预测值；最后一个实测点在两个 series 里都出现，线才连得上
// 锚点用 markPoint 标出，读者能看到模型对齐了哪些实测值
```

### 横向柱状图（分类对比，动态着色）

```js
// pairs = cats.map(function(c,i){return {k:c,v:vals[i]}}).sort(function(a,b){return b.v-a.v})
grid:{left:40,right:50,top:8,bottom:20},
yAxis:{type:'category',data:pairs.map(function(p){return p.k})},  // category 在 y
xAxis:{type:'value',axisLabel:{formatter:'{value}%'}},              // value 在 x
series:[{type:'bar',data:pairs.map(function(p){return p.v}),
  itemStyle:{color:function(p){var v=p.value;return v>=50?'#10b981':(v>=45?'#6366f1':'#f59e0b')},
   borderRadius:[0,4,4,0]},
  barWidth:'65%',label:{show:true,position:'right',fontSize:11,formatter:'{c}%',fontWeight:600}}]
```

横向柱状图的 axis 类型和竖向是**互换**的，容易搞错。分类项先排序再画。

---

## UI 坑点

1. **script src 与 inline 互斥** — `<script src="...echarts">` 带 `src` 时，标签内的 inline 代码被浏览器忽略。图表 init 必须放独立无 `src` 的 `<script>` 块。
2. **Tab 切换 resize 至少 200ms** — 非激活 Tab 里的图表宽度为 0，切过去要 `setTimeout` resize，80ms 不够。
3. **DOM ID 冲突** — 改内容区后 grep `id="ch-` / `id="funnel-` 确认无重复。旧占位段不删会让图表渲染到错误的空容器。
4. **每个图表容器都要有 init** — grep 出所有 `id="ch-xxx"`，逐个对照是否有对应 init 代码，且在同一作用域。
5. **CSS 变量不硬编码** — 颜色一律走 `var(--xxx)`，例外只有需要动态着色的 ECharts itemStyle。
6. **横向柱状图 axis 互换** — yAxis=category、xAxis=value。
7. **road-time 必填** — 缺了卡片高度不齐。
8. **状态卡最后一张去 margin-bottom** — 否则底部多余留白。
9. **图表标注遮挡** — label + markLine + markPoint 三者同时开容易叠在一起，密集数据只留一种。
10. **表格样式随内容丢失** — 用 `<div class="table-scroll">` 包裹后确认 `th`/`td` 样式仍生效，不要在 table 上再套 flex 容器。
11. **f-string 生成 JS 时的引号** — Python f-string 里拼 JS 字符串，内外引号必须错开（外 `"""`，JS 内用单引号），否则注入的字符串截断。
12. **数字千分位** — 大数用 `toLocaleString()`，别让读者数零。

---

## 交付前自检

浏览器打开后三查：

1. 逐个切 Tab，看有没有空白图表区
2. DevTools Console 无红色报错
3. `grep 'id="ch-' report.html` 对照每个容器都有 init

再加一条数据侧的：**HTML 里的数字与落盘 json/文档中的数字逐项对齐**，不一致就是有一处手抄过。
