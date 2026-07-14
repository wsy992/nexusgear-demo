<h1 align="center">NEXUS GEAR</h1>
<h3 align="center">沉浸式 O2O 电竞潮玩定制平台</h3>

<p align="center">
  <b>面向 18–35 岁硬核玩家的电竞外设电商平台</b><br>
  融合 C2M AI 反向定制 · 外设 DNA 图谱 · 荷兰式动态拍卖 · ESG 循环经济
</p>

<p align="center">
  <a href="https://wsy992.github.io/nexusgear-demo/"><strong>🖥️ 在线 Demo</strong></a>
  ·
  <a href="#-技术实现">技术实现</a>
  ·
  <a href="#-核心功能">核心功能</a>
  ·
  <a href="#-业务流程">业务流程</a>
</p>

---

## 📖 项目概述

NEXUSGEAR 是一个面向电竞外设市场的**沉浸式 O2O 电子商务平台**概念验证项目，为澳门城市大学电子商务课程期末作业。平台采用「前店后仓」双线融合模式，线上提供 3D 虚拟定制车间，线下设置实体体验门店，通过八大核心功能覆盖用户从发现、决策、购买到社交与售后的完整旅程。

本项目在理论层面综合运用 **PAD 情绪模型**、**SOR 刺激-有机体-反应框架**、**长尾理论**与**价格锚定效应**等行为经济学理论；在实现层面以一份 **~3,300 行纯 HTML/CSS/JavaScript 单文件 Demo** 验证全部核心交互的可行性，所有代码为本组独立原创。

---

## 🛠️ 技术实现

```
nexusgear-demo/
├── index.html            # 单文件全功能 Demo（~3,300 行）
├── assets/
│   ├── stock/            # 商品图片素材（键盘/鼠标/耳机/电竞椅等）
│   └── remote/           # 远程 CDN 素材
└── screenshots/          # 页面截图
```

- **纯原生前端技术栈**：HTML + CSS + JavaScript 实现完整电商前端逻辑，涵盖路由切换、状态管理与交互动画全链路
- **赛博朋克视觉风格**：深色主题 + 霓虹渐变 + CSS 动画（网格移动 / 粒子爆炸 / 3D 旋转立方体 / 开箱动画）
- **SPA 架构**：7 个页面即时切换无白屏刷新，浏览器原生实现类原生 APP 交互体验
- **原创+AI辅助**：个人独立编写代码，结合 Claude Code 辅助完成架构思路梳理与样式打磨

### 代码亮点

**CSS 设计系统 — 霓虹赛博朋克主题变量**：

```css
:root {
  --bg-primary: #070711;      /* 深空底色 */
  --cyan: #00f5ff;            /* 霓虹青 — 主色调 */
  --pink: #ff0090;            /* 霓虹粉 — 强调色 */
  --purple: #7b2fff;          /* 霓虹紫 — 渐变辅色 */
  --yellow: #ffd700;          /* 金色 — 拍卖价格高亮 */
  --text-primary: #e8eaf6;    /* 主文字色 — 浅灰白 */
  --text-secondary: #8892a4;  /* 辅助文字色 */
  --border: rgba(0,245,255,0.12);  /* 微光边框 */
  --shadow-cyan: 0 0 24px rgba(0,245,255,0.35); /* 发光阴影 */
}
```

**荷兰式拍卖 — 实时倒计时与递减定价**：
```javascript
function startAucTimer(item, idx) {
  const TOTAL = 600; // 10分钟一个拍卖周期
  let elapsed = idx * 120; // 三个拍品错峰开始，间隔2分钟
  const decrement = Math.round((item.start - item.floor) / (TOTAL / 10)); // 每10秒降价步长

  aucIntervals[item.id] = setInterval(() => {
    // 已被抢购则停止倒计时
    if (aucState[item.id].sold) { clearInterval(aucIntervals[item.id]); return; }
    elapsed++;
    const remaining = TOTAL - (elapsed % TOTAL);
    const mins = String(Math.floor(remaining / 60)).padStart(2, '0');
    const secs = String(remaining % 60).padStart(2, '0');
    document.getElementById(`auc-timer-${item.id}`).textContent = `${mins}:${secs}`;

    // 每10秒触发一次降价，不低于底价
    if (elapsed % 10 === 0) {
      aucState[item.id].cur = Math.max(item.floor, aucState[item.id].cur - decrement);
      document.getElementById(`auc-price-${item.id}`).textContent =
        `¥${aucState[item.id].cur.toLocaleString()}`;
    }
  }, 1000); // 每秒刷新
}
```

**DNA 五维雷达图 — 纯 SVG 动态生成**：
```javascript
function generateRadarSVG(scores, labels) {
  const cx = 130, cy = 130, r = 90; // 圆心坐标 & 半径
  const angleStep = (Math.PI * 2) / scores.length; // 五等分圆周
  // 绘制4层同心网格（25%/50%/75%/100%）
  const grid = [0.25,0.5,0.75,1].map(frac => {
    const pts = Array.from({length:scores.length}, (_,i) => {
      const a = -Math.PI/2 + i * angleStep; // 从正上方开始绘制
      return `${cx + r*frac*Math.cos(a)},${cy + r*frac*Math.sin(a)}`;
    }).join(' ');
    return `<polygon points="${pts}" fill="none" stroke="rgba(255,255,255,0.1)" stroke-width="1"/>`;
  }).join('');
  // 根据五项得分计算数据多边形顶点坐标
  const dataPts = Array.from({length:scores.length}, (_,i) => {
    const a = -Math.PI/2 + i * angleStep;
    const rv = r * scores[i] / 100;
    return `${cx + rv*Math.cos(a)},${cy + rv*Math.sin(a)}`;
  }).join(' ');
  return `<svg viewBox="0 0 260 260">${grid}
    <polygon points="${dataPts}" fill="rgba(0,245,255,0.15)" stroke="rgba(0,245,255,0.8)" stroke-width="2"/>
  </svg>`;
}
```

**购物车状态管理 — 实现增删改与实时计价**：
```javascript
function pushCart(p) {
  const ex = cart.find(x => x.id === p.id);
  ex ? ex.qty++ : cart.push({...p, qty: 1}); // 已存在则累计，否则新增
  renderCart();
}
function renderCart() {
  const tot = cart.reduce((s, x) => s + x.qty, 0);
  const badge = document.getElementById('cartBadge');
  badge.textContent = tot;
  badge.style.display = tot > 0 ? 'flex' : 'none'; // 角标显隐
  if (!cart.length) { /* 空状态UI */ return; }
  // 渲染每件商品（含数量增减按钮与删除）
  document.getElementById('cartTotal').textContent =
    '¥' + cart.reduce((s, x) => s + x.price * x.qty, 0).toLocaleString();
}
function chgQty(id, d) {
  const x = cart.find(i => i.id === id);
  if (!x) return;
  x.qty += d;
  if (x.qty <= 0) cart = cart.filter(i => i.id !== id); // 数量归零则移除
  renderCart();
}
```


> 完整源码 3,600+ 行见 [`index.html`](https://github.com/wsy992/nexusgear-demo/blob/main/index.html)

---

## ✨ 核心功能

### 🎯 三大创新模块

| 模块 | 说明 |
|------|------|
| **🧬 外设 DNA 图谱** | 用户完成 5 题问卷，即时生成 SVG 五维雷达图（精准操控 / 手感舒适 / 颜值外观 / 低延迟竞技 / 沉浸音效），AI 计算全库商品兼容度并输出 TOP3 推荐组合。DNA 数据持久绑定，随消费行为持续更新，是平台最核心的用户留存机制。 |
| **⏳ 荷兰式限定拍卖** | 限定款外设独立 10 分钟倒计时，价格每 10 秒自动递减；页面实时显示围观人数与库存稀缺标签；首位点击「立即抢购」的用户以当前价格锁单。同时激活**价格锚定效应**与**竞争紧迫感**，驱动冲动消费决策。 |
| **♻️ 以旧换新循环经济** | 用户通过 4 步表单完成在线 AI 残值估算，获得专属折抵券（券面价值比现金回收高 10%）；旧设备寄回后经 AI 质检翻新，低价上架二手区。单件翻新品平均减少约 **2.3 kg CO₂** 排放，是平台 ESG 可持续承诺的核心载体。 |

### 🔧 五项支撑功能

- **📈 实时行情 Ticker** — 金融行情风格滚动条，7 款外设价格实时波动，红涨绿跌，激活价格焦虑与即时决策
- **🎨 3D 定制车间** — 颜色切换 + 激光刻字实时预览，所见即所得的个性化定制体验
- **🤖 C2M AI 智能推荐** — 基于浏览/点击行为 + DNA 图谱的协同过滤推荐，用户行为数据反哺工厂按需生产
- **💬 玩家社区** — 瀑布流 UGC 桌搭展示，帖子直接关联同款商品，「内容即电商」的种草到拔草闭环
- **🎁 赛博礼盒** — 开箱动画 + 社交分享 + 送礼人与收礼人地址分离发货（借鉴微信蓝包模式），每次送礼即一次品牌传播

---

## 🔄 业务流程

平台设计了五条核心业务流程，相互嵌套形成数据驱动的增长飞轮：

```
DNA 图谱测试 → AI 精准推荐 → 荷兰式拍卖购买
                                    ↓
                              旧设备触发以旧换新
                                    ↓
                            AI 估价 → 翻新再售 → 新用户购入
                                    ↓
                           换新后更新 DNA → 新一轮推荐
```

| 流程 | 核心节点数 | 说明 |
|------|:--------:|------|
| O2O 主购物流程（自购 / 送礼双分支） | 8 | 覆盖 3D 定制 → 动态计价 → 支付 → O2O 极速配送（24–48h） |
| 赛博礼盒社交裂变 | 8 | 买家选品 → 生成礼盒码 → 社交分享 → 收礼人独立填地址 → 平台直发 |
| C2M AI 反向定制 | 7 | 用户行为采集 → AI 需求聚合 → 代工厂小批量生产 → 精准推送 |
| 以旧换新循环 | 6 | 在线估价 → 折抵券 → 回收 → AI 质检翻新 → 二手区上架 |
| 外设 DNA 图谱生成 | 6 | 5 题问卷 → AI 加权 → 雷达图 → 兼容度评分 → TOP3 推荐 |

> 完整流程图请参见 [`screenshots/`](screenshots/) 目录。

---

## 🎓 理论支撑

本项目的功能与营销策略设计建立在以下行为经济学与电子商务理论之上：

| 理论 | 在平台中的应用 |
|------|--------------|
| **PAD 情绪模型** | 荷兰式拍卖 + DNA 测试制造「高兴奋度 + 低控制感」，驱动冲动消费 |
| **SOR 框架** | 行情 Ticker 作为 Stimulus（S），触发情绪反应（O），导向下单行为（R） |
| **价格锚定效应** | 原价划线 + 拍卖实时价并列呈现，制造强烈价值落差感知 |
| **长尾理论** | C2M 反向定制聚合长尾需求，小批量生产覆盖利基市场 |
| **ESG 循环经济** | 以旧换新减少电子垃圾，减碳数据标注于产品页，吸引 Z 世代消费者 |

---

## 📊 市场策略（四维框架）

1. **定价** — 荷兰式动态拍卖 + 价格锚定 + 青铜/白银/黄金/钻石四级会员 + 以旧换新折抵券叠加
2. **内容营销** — KOL/KOC 测评 + UGC 桌搭社区双轮驱动 + 赛博礼盒社交裂变 + 小红书/抖音/微博矩阵
3. **用户留存** — 积分体系 + DNA 图谱长期绑定 + 会员早鸟价 + Ticker 行情刺激
4. **ESG 可持续** — 以旧换新闭环 + 年度减碳披露 + Z 世代绿色品牌认同溢价

---

## 📄 许可与致谢

- 学术报告、PPT 讲稿、流程图等完整材料见课程提交版本
- 商品图片素材来源于网络，仅供学术 Demo 展示使用

