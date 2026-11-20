# style_2 · 像素 / 16-bit 街机

> **一句话**：整页只有两种边（2px 和 1px）、一种圆角（0）、一种缓动（跳），用有限的颜色和整像素的位移，把界面做成一台老机器的截图。
>
> **和 `style_1` 的区别**：`style_1` 的高级感来自**连续性**——模糊、羽化、渐变、缓动、亚像素；这一种正好相反，来自**离散性**——硬边、台阶、色阶、跳帧、整像素。两种风格的技术难点是镜像的：玻璃怕糊得不够，像素怕糊得过头。

| | |
|---|---|
| 文件 | [`index.html`](index.html) —— 单文件，双击即可打开 |
| 依赖 | **零网络请求**。像素字体以 base64 内联在 HTML 里，图标、吉祥物、地平线全是代码画的 |
| 技术 | HTML + CSS 自定义属性 + `<canvas>` 精灵引擎（约 630 行原生 JS，零第三方库） |
| 主题 | 白天 / 夜晚两套**配色**（不是明暗），跟随系统，可手动切换并记住 |
| 切换动效 | View Transition 快照 + `clip-path` 分 18 帧横向刷出 |
| 可调项 | 右下角 OPTIONS：阴影深度 / 扫描线 / 帧率 / 强调色 |

**适合**：个人主页、游戏 / 工具类产品落地页、活动页、复古主题的后台、任何想让人「一眼记住」的页面。
**不适合**：密集数据表格（2px 边框会吃掉对齐感）、长文阅读（全大写像素字体不适合正文）、需要传达「轻、快、未来」的品牌。

---

## 视觉配方

所有值都在 `index.html` 顶部。改这里就够了。

### 两套配色

不是明暗，是两个场景：**白天 = 羊皮纸**，**夜晚 = 靛蓝夜空**。两套的色相都控制在同一个家族里（暖黄 + 靛蓝 + 一个强调色），所以换主题像换了一张调色板，而不是换了一个网站。

| 令牌 | 夜晚 | 白天 | 作用 |
|---|---|---|---|
| `--ink` | `#0a0c14` | `#251a10` | 描边、投影、文字。**全站唯一的「黑」** |
| `--bg` / `--bg-2` | `#12162a` / `#171d33` | `#f2e5c6` / `#ecdcb8` | 页面底色 / 顶栏页脚底色 |
| `--panel` / `--panel-2` | `#202844` / `#2a3457` | `#fbf2db` / `#fff8e7` | 面板底 / 面板高亮（标题栏、输入底） |
| `--edge` / `--edge-hi` | `#41507f` / `#7183bd` | `#bfa471` / `#fffdf4` | 内斜面：右下背光 / 左上受光 |
| `--line` | `#2f3a5f` | `#d8c49a` | 1px 分隔线（列表、统计行） |
| `--text` / `--text-2` | `#f6ecd6` / `#c6bda9` | `#2a1e12` / `#57462f` | 正文 / 次级正文 |
| `--mute` | `#8b93b6` | `#6f5c44` | 标签、日期、说明 |
| `--shadow` | `rgba(5,7,16,.72)` | `rgba(83,58,22,.42)` | 硬投影的颜色 |
| `--wall-1…4` | `#080b17 → #232c50` | `#f8eecd → #e3cb9d` | 天空的四段色阶 |
| `--hill-far` / `--hill-near` | `#1a2140` / `#0d1224` | `#e7d3a6` / `#d9c08a` | 两道山脊 |

强调色三选一，**两套主题各自校正过明度**（夜里更亮、白天更深），否则同一个橙在羊皮纸上会发飘：

| `data-accent` | 夜晚 | 白天 | 文字压在强调色上 |
|---|---|---|---|
| `amber` | `#ffb43f` | `#c96a12` | `#231806` / `#241806` |
| `mint` | `#4fe0b0` | `#0b8368` | `#06231a` / `#f2fffb` |
| `rose` | `#ff7a9c` | `#bb3159` | `#2a0713` / `#fff3f6` |

### 共用的度量

```
--wrap     1180px         内容最大宽度（顶栏与正文用同一个，边缘才对得齐）
--hud-h    66px           顶栏高度；≤900px 时变成 104px（两行）
--bw       2px            描边厚度，全站只有 2px 和 1px 两种
--depth    6px            硬投影的外扩距离；OPTIONS 里可切 0 / 4 / 6 / 8
--scan     .055           扫描线浓度：0 / .055 / .14
--dur      140 / 280 / 520ms    状态 / 组件 / 版块
--f        steps(3) / steps(4) / steps(8)   ← 注意不是 cubic-bezier
圆角       0（一个都没有）
间距       8 的倍数：8 / 12 / 16 / 22 / 28 / 56 / 84
```

三档帧率只是换一组时长与步数，`data-fps` 一挂上去所有动效同时变速：

```css
html[data-fps="12"]{ --dur-1:240ms; --f-1:steps(2,end); --f-3:steps(5,end); }
html[data-fps="24"]{ --dur-1:140ms; --f-1:steps(3,end); --f-3:steps(8,end); }
html[data-fps="60"]{ --dur-1:90ms;  --f-1:steps(6,end); --f-3:steps(12,end); }
```

### 字节（字体与尺度）

```
拉丁 / 数字  Silkscreen 400 & 700（base64 内联，SIL OFL 1.1，见 OFL-Silkscreen.txt）
中文        PingFang SC → Hiragino Sans GB → Source Han Sans SC → Microsoft YaHei → system-ui
正文        16px / 1.75
大标题      clamp(36px, 5.4vw, 62px)，字重 800，行高 1.16，硬投影 6px
版块标题    clamp(21px, 2.5vw, 29px)
像素小字    9–13px，全大写，字距 .10–.18em（Silkscreen 只在整数 px 下才锐利）
```

**为什么只让拉丁字母用像素字体**：这套页面是中文的，而 5×5 的点阵里画不出汉字。与其硬造一套像素中文字体，不如让两种字各司其职 —— 中文用系统黑体承担**阅读**，拉丁字母、数字、标签用 Silkscreen 承担**质感**。这也是真实的老游戏本地化做法：UI 字体是点阵，正文是另一套。

---

## 结构分层

```
5  UI 层     nav / main / footer / OPTIONS —— 全部 2px 描边、零圆角
4  精灵层    <canvas> 里画出图标、吉祥物、缩略图（整数倍放大）
3  扫描线    .scan  repeating-linear-gradient(0deg, 黑 1px, 透明 2px)
2  地平线    #landscape  4px 一个画布像素，山脊台阶 8px
1  天空      .wall  四段色阶 + 4px 抖动网点
```

### 天空：用网点代替渐变

平滑渐变在这个风格里是错的 —— 它会产生 16-bit 机器上不可能有的连续色阶。所以：**色阶照旧分段，段与段之间叠一层棋盘格**。

```css
.wall{
  background:
    radial-gradient(120% 78% at 76% 4%, rgba(255,180,63,.10), transparent 58%),
    linear-gradient(180deg,var(--wall-1) 0%,var(--wall-2) 44%,var(--wall-3) 76%,var(--wall-4) 100%);
}
.wall::before{
  content:"";
  position:absolute; inset:0;
  background-image:repeating-conic-gradient(from 0deg,
    rgba(255,255,255,.055) 0 25%,
    rgba(255,255,255,0) 0 50%);
  background-size:4px 4px;     /* 一个格子 4px，里面两个 2px 方块 */
  opacity:.75;
}
```

`repeating-conic-gradient` 的 25% / 50% 两个止点，在一个 4px 的格子里切出**四个象限**，相邻象限一亮一暗，就是最经典的有序抖动（ordered dither）。

### 地平线：把 canvas 当调色板用

`#landscape` 的尺寸不按 CSS 像素设，而按 **4 CSS px = 1 画布像素**：宽 1452 的视口 → `canvas.width = 363`。这样每个画布像素被浏览器放大 4 倍，天然是方块，不需要任何滤镜。

山脊的采样步长是 2 个单位，高度 `Math.round(h/2)*2` —— **台阶永远是 8px**，不会出现 4px 的半级台阶，看起来才像同一台机器画的。

---

## 核心技术点

### 1. 硬边材质：一套公式，四种投影

```css
.px{                                   /* 面板 */
  background:var(--panel);
  border:var(--bw) solid var(--ink);
  box-shadow:
    inset 2px 2px 0 0 var(--edge-hi),  /* 左上受光 */
    inset -2px -2px 0 0 var(--edge),   /* 右下背光 */
    var(--depth) var(--depth) 0 0 var(--shadow);   /* 外扩投影，零模糊 */
}
.px-btn{                               /* 按钮：再加一层「脚」 */
  box-shadow:
    inset 2px 2px 0 0 var(--edge-hi),
    inset -2px -2px 0 0 var(--edge),
    0 4px 0 0 var(--ink);
  transition:transform var(--dur-1) var(--f-1), box-shadow var(--dur-1) var(--f-1);
}
.px-btn:hover{ transform:translateY(-2px); }   /* 抬起来 → 影子变长 */
.px-btn:active{ transform:translateY(3px); }   /* 按下去 → 影子几乎没了 */
```

四个要点：

1. **内斜面用两层 `inset`**，不用 `border-color` 的四色写法。`inset` 不吃盒模型，圆角/描边都还归你管。
2. **外投影的模糊半径必须是 0**。`0 0 6px rgba(...)` 是 Material，`6px 6px 0 rgba(...)` 才是 16-bit。
3. **`--depth` 同时驱动标题的 `text-shadow`**：调「阴影深度」时，面板和大标题一起变平或一起变厚，观感才统一。
4. **按钮的「按下」是真的位移 3px**，不是 `scale(.98)`；缩放会插值出亚像素，像素风里那是脏的。

### 2. 字体：内联 base64，且只喂整数 px

```css
@font-face{
  font-family:"Silkscreen"; font-weight:400; font-display:block;
  src:url(data:font/woff2;base64,…) format("woff2");
}
```

- 用 base64 而不是 `@import`：页面依旧是**一个文件、零网络请求**，断网、丢到任何静态服务器、直接双击打开都一样。
- `font-display:block` 而不是 `swap`：字形就在文档里，不存在等待，换成 `swap` 反而可能闪一帧系统字体。
- 像素字体只在**整数字号**下锐利。9 / 10 / 11 / 13px 是量过的档位，`10.5px` 会立刻发虚。
- 中文字体栈放在 `--sans` 里，像素字体只挂在 `--pix` 上，由 `.pix` / 各种小标签显式使用 —— 不做全局替换。

### 3. 精灵引擎：字符串矩阵 → canvas

图标、吉祥物、缩略图不是 SVG、不是 emoji、不是图片，是**字符串**：

```js
cat: [
  '................',
  '..kk........kk..',
  '.kffk......kffk.',
  …
]
```

一个字符 = 一个调色板下标（`k` 轮廓、`f` 毛、`e` 眼窝、`p` 耳内……），`compile()` 把非空的格子收进数组，`paint()` 用 `fillRect` 逐格画：

```js
function paint(cv, sp, scale){
  var u = Math.max(1, Math.round(scale));     // 只允许整数倍
  cv.width  = sp.w * u * DPR;                 // 设备像素也是整数倍
  cv.height = sp.h * u * DPR;
  cv.style.width  = (sp.w * u) + 'px';        // CSS 尺寸跟设备像素解耦
  cv.style.height = (sp.h * u) + 'px';
  var px = u * DPR;
  sp.cells.forEach(c => { ctx.fillStyle = P[c.c]; ctx.fillRect(c.x*px, c.y*px, px, px); });
}
```

三个坑：

1. **`DPR` 要取整**。`devicePixelRatio` 在 Windows 上常是 `1.25 / 1.5`，直接用会让每个像素落在半个设备像素上，整张图糊掉。这里 `Math.round()` 一下，1.5 按 2 处理。
2. **`cv.width` 是设备像素，`cv.style.width` 是 CSS 像素**，两个都要设，否则要么糊要么尺寸错。
3. **`image-rendering:pixelated` 只是保险**。真正让它锐利的是「整数倍 + 整数设备像素」，这个滤镜救不了非整数缩放。

### 4. 会看你的猫

吉祥物是一个 16×16 的猫头，`'e'` 标记眼窝。运行时扫出左右两组眼窝的包围盒，然后：

```js
// 眨眼：先用毛色盖住眼窝，再补一条横线
ctx.fillStyle = P.f; ctx.fillRect(eye.x0*px, eye.y0*px, w, h);
ctx.fillStyle = P.k; ctx.fillRect(eye.x0*px, eye.y1*px, w, px);

// 看人：瞳孔在 3 格宽的眼窝里只能左右挪 1 格
var mid = Math.round((eye.x0 + eye.x1) / 2) + look;   // look ∈ {-1,0,1}
ctx.fillRect(mid*px, eye.y0*px, px, h);
```

指针移动时算一次「在猫左边 / 中间 / 右边」，只在取值变化时重绘；再随机 2.4–6.6s 眨一次眼。因为眼窝是从精灵数据里**扫**出来的，改猫脸不需要改代码。

### 5. 换肤：让新画面一条一条刷上来

`style_1` 的擦除是**羽化的斜向遮罩**（连续），这里的刷屏必须**分段**（离散）：

```css
::view-transition-old(root){ animation:none; z-index:1; }   /* 旧画面一直垫在下面 */
::view-transition-new(root){
  z-index:2;
  animation:vt-scan var(--dur-3) steps(18,end) both;
}
@keyframes vt-scan{
  from{ clip-path:inset(0 0 100% 0); }
  to{ clip-path:inset(0 0 0 0); }
}
```

`steps(18)` 是关键：`clip-path` 本身会平滑插值，加上 18 步之后，新主题像老电视的行同步一样**一格一格往下刷**，每一格高度恒定。

配套两件事：

- 换肤那一帧给 `<html>` 加 `.no-anim`（`transition:none !important`），否则颜色会在快照结束后继续爬 300ms。
- `vt.finished.then(…)` 里再移除，且用 `requestAnimationFrame` 包一层，避免和快照提交抢同一帧。

### 6. 动效量化：全站只有一种缓动

```css
--f-1:steps(3,end);   /* hover / focus */
--f-2:steps(4,end);   /* 面板、卡片 */
--f-3:steps(8,end);   /* 入场、换肤 */
```

- 像素风里，`cubic-bezier` 会插出中间态颜色和位置，看起来像「高清视频里的像素皮肤」。`steps()` 才是同一种介质。
- 但**不是越多步越好**：hover 用 3 步（一帧 46ms）刚好读作「跳了一下」；8 步以上就开始像卡顿了。
- OPTIONS 的「帧率」三档就是换步数：12fps 只有 2 步，60fps 有 12 步 —— 同一个界面，三种年代感。

### 7. 跑马灯：每一跳都落在像素边界上

百分比位移 + `steps()` 是不可靠的（每一跳的距离不是整数）。这里先量出**一条列表的像素宽**，再算出「一跳 8px」需要多少步：

```js
var w = Math.round(first.getBoundingClientRect().width);
var steps = Math.max(16, Math.round(w / 8));
track.style.setProperty('--tick-w', w + 'px');       /* 位移距离是 px，不是 % */
track.style.setProperty('--tick-steps', steps);
track.style.setProperty('--tick-dur', (steps * 0.17).toFixed(2) + 's');
```

```css
.ticker-track{ animation:tick var(--tick-dur) steps(var(--tick-steps),end) infinite; }
@keyframes tick{ to{ transform:translate3d(calc(-1 * var(--tick-w)),0,0); } }
```

内容复制一份、位移正好等于一份的宽度，接缝永远对得上；窗口缩放时重算一次。

### 8. 统计条 / 进度条：把「百分比」画成格子

不用 `<progress>`、不用宽度渐变的 div，用**重复渐变**把一条实心色切成一格格：

```css
.hud-prog i{
  background:repeating-linear-gradient(90deg,var(--accent) 0 6px,transparent 6px 10px);
  transition:width 100ms steps(6,end);
}
```

角色状态条则是 5 个真实 `<i>`，多了就 `nth-child(-n+4)` 点亮 —— 因为它是**语义**上的 5 档，不是一条连续进度。

### 9. 一个真实的坑：中文标点后面的空格

源码里换行会变成一个空格，中文排版里它会变成一个诡异的空当：

```html
<p>…视觉系统。
   像素风格不是怀旧…</p>   <!-- 渲染成「视觉系统。 像素风格」 -->
```

解决办法只有两个：整段写在一行里（本项目采用），或者在换行处塞 `<!-- -->`。

---

## 动效清单

| 元素 | 触发 | 时长 | 曲线 | 变化 |
|---|---|---|---|---|
| 换肤刷屏 | 点切换 | 520ms（随帧率） | `steps(18)` | 新画面 18 条横向刷下 |
| 卡片悬停 | hover | 280ms | `steps(4)` | 左移上移 2px，投影 6→10px，缩略图精灵上移 4px |
| 按钮悬停 | hover | 140ms | `steps(3)` | 上移 2px，脚从 4px 变 6px，箭头右移 3px |
| 按钮按下 | active | 140ms | `steps(3)` | 下沉 3px，脚剩 1px |
| 导航当前项 | 滚动 | 即时 | — | 出现 ▶ 记号 + 2px 强调色下边线 |
| 跑马灯 | 常驻 | 每跳 170ms | `steps()` | 每跳位移 8px，悬停暂停 |
| 猫 | 指针 / 随机 | 即时 / 130ms | — | 瞳孔左右各 1px；2.4–6.6s 眨一次 |
| 入场 | 进入视口 | 520ms | `steps(8)` | 上移 10px + 淡入，卡片错峰 70ms |
| 状态点 | 常驻 | 1.4s | `steps(1)` | 方块的亮灭切换（不是淡入淡出） |
| 进度条 | 滚动 | 100ms | `steps(6)` | 宽度按滚动比例增长，格子状填充 |

---

## 降级与可访问性

| 情况 | 处理 |
|---|---|
| 不支持 View Transitions | 直接换主题（`commit()` 同步执行），颜色正确、没有刷屏动画 |
| 不支持 `<canvas>` | 所有精灵与地平线消失，文字与布局完整（`canvas` 都带 `aria-hidden`，本来就不是信息） |
| `prefers-reduced-motion` | 不做刷屏、不眨眼、停跑马灯、关入场；`transition-duration:.001ms` 全站兜底 |
| 禁用 JS | `.rv` 的隐藏挂在 `.js` 类下，禁用 JS 时全部可见；精灵不画，但没有任何内容依赖它们 |
| 键盘 | 首个可聚焦元素是「跳到正文」；`:focus-visible` 是 3px 强调色描边 + 2px offset；`Esc` 关 OPTIONS |
| 主题控件 | `role="radiogroup"` + `role="radio"` + `aria-checked`（这是单选，不是开关） |
| 复制反馈 | `role="status" aria-live="polite"` 的 toast；`clipboard` 失败时回退 `execCommand` 并改成「邮箱是 …」 |
| 读屏 | 装饰层（天空 / 扫描线 / 地平线 / 精灵 / 窗口小圆点 / 序号）全部 `aria-hidden`；章节标题是真实 `<h2>` |
| 对比度 | 正文 `--text` 在两套主题下 ≥ 12:1；`--mute` ≥ 4.6:1；强调色与 `--accent-ink` ≥ 7:1 |
| 系统外观变化 | 用户没手动选过时自动跟随（`matchMedia('(prefers-color-scheme: light)')`） |
| 触控 | 可点区域最小 36×28（主题按钮）/ 44px 高（按钮）；≤880px 顶栏换两行 |

---

## 常见错误

按「做出来为什么不对劲」排序：

1. **加了圆角。** 一个 4px 圆角就足以让整页从 16-bit 掉回 2020 年的 SaaS 模板。要圆就用像素阶梯（`clip-path` 或精灵）画。
2. **投影带模糊。** `0 8px 24px rgba(0,0,0,.4)` 是 Material；像素风只有硬投影，模糊半径必须是 0。
3. **精灵用小数倍放大。** `scale(2.5)` 或 `width:71px` 会让每个像素宽窄不一，眼睛立刻看出「这不是画出来的」。
4. **像素字体拿去排正文。** Silkscreen 排中文会掉回系统字体，两种字混在一行里，节奏全乱。
5. **像素字体用小数号。** `11.5px` 会触发浏览器对字形的缩放，笔画粗细不匀。
6. **`cubic-bezier` 混进来。** 哪怕只有一处，观感就变成「高清页面套了层像素滤镜」。
7. **颜色太多。** 这套风格的力气来自「没得选」。一共 4 个中性阶 + 1 个强调色就够；再加就是普通网页了。
8. **抖动网点用在高对比处。** 网点只在**大面积同色**上好看，压在文字背后就是噪点。
9. **扫描线开太浓。** `.14` 以上会让小字号发糊；默认 `.055`，且给用户关掉的开关。
10. **`canvas` 忘了同步 `style.width`。** 只设 `width` 属性，元素会被拉成默认 300×150。
11. **`DPR` 不取整。** Windows 的 `1.5` 会把所有精灵画糊，且只在部分机器上出现，最难查。
12. **跑马灯用百分比位移配 `steps()`。** 每一跳的距离不是整数，越跑越抖；要先量宽度。
13. **换肤时没关过渡。** 颜色会在刷屏结束后继续爬，露出「还在动」的破绽。
14. **把中文段落写成多行。** 每个换行都会变成一个空格，中文里那是排版事故。

---

## 怎么改

| 想改什么 | 动哪里 |
|---|---|
| 换强调色 | `--accent` / `--accent-2` / `--accent-ink`（两套主题各一遍）；OPTIONS 里已有三套，加第四套照抄一组 |
| 换场景（比如「黄昏」「深海」） | `--wall-1…4`、`--hill-far/near`、`--panel*`、`--edge*` 一起换，别只换天空 |
| 更扁平 / 更厚 | `--depth`（0 完全平，8 很厚）；也会同时改标题的硬投影 |
| 更像 CRT | `--scan` 调到 `.14`，或 `data-scan="strong"` |
| 更「现代」一点（慎用） | `--f-1..3` 从 `steps()` 换成 `cubic-bezier(.32,.72,0,1)` —— 会立刻不像像素风，但确实更顺 |
| 让动效更顿 | OPTIONS 切 12fps，等于把三档时长和步数一起放大 |
| 换吉祥物 | 改 `SPR.cat` 的 16 行字符串；眼窝用 `'e'` 标出来，瞳孔逻辑自动适配 |
| 换缩略图 | 改 `SPR.monitor / cart / pad / disk`，`data-sprite` 与 `data-scale` 在 HTML 里 |
| 换手感（间距） | 所有间距都是 8 的倍数；先改 `--wrap` 与版块 `padding-block`，别逐条改 |
| 加一个版块 | 复制 `.sec` + `.sec-head`，索引序号改成 `04`，`id` 同步到导航 |

> 每加一个精灵，记得重新跑一遍「每行等宽」的检查（`SPR` 里所有行长度必须一致），否则 `fillRect` 会画错位。

---

## 文件说明

```
src/style_2/
├── index.html          # 页面 + 内联样式（1330 行）+ 内联脚本（630 行）
├── README.md           # 本文件
└── OFL-Silkscreen.txt  # 内联字体 Silkscreen 的 OFL 1.1 授权全文
```

`index.html` 的阅读顺序：

1. `<head>` 里的内联脚本 —— 主题、强调色、阴影、扫描线、帧率，在第一次绘制前定下来
2. `@font-face` 两段 base64 —— 字体就在文件里
3. `:root` 与两套配色 —— 全部令牌
4. `/* ── 基底 */` → `/* ── 背景三层 */` → 各组件
5. `/* ── 换肤：一条一条横向刷上去 ── */` —— View Transition 那一段
6. 文件末尾的 `<script>` —— 精灵引擎 → 地平线 → 选项 → 滚动 → 入场 → 跑马灯 → 复制

← 回到 [项目说明](../../README.md)
