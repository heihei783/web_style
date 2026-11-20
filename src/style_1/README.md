# style_1 · 苹果式清晰毛玻璃

> **一句话**：像 macOS 的侧边栏那样——一层薄薄的、会透出背后颜色的玻璃，上面放着清清楚楚的内容。
>
> **和「彩虹毛玻璃」的区别**：不清透的毛玻璃通常是「玻璃后面没有东西可糊」。苹果的玻璃之所以清，不是因为它更白，而是因为**壁纸本身颜色很足**，玻璃把那些颜色模糊、提亮、加饱和之后透出来。玻璃的质感来自背景，不来自玻璃自己。

| | |
|---|---|
| 文件 | [`index.html`](index.html) —— 单文件，双击即可打开 |
| 依赖 | **零**。不加载字体、不加载图标，断网也完整 |
| 技术 | HTML + CSS 自定义属性 + 原生 JS（约 340 行） |
| 主题 | 浅色 / 深色双主题，跟随系统，可手动切换并记住 |
| 切换动效 | 由左上角斜向铺到右下角的羽化擦除 |

**适合**：个人主页、产品落地页、SaaS 后台、设置面板、任何需要「浮起来」的卡片式界面。
**不适合**：密集数据表格（模糊吃掉对齐感）、长文阅读页（半透明底累眼）、打印。

---

## 视觉配方

所有值都在 `index.html` 顶部。改这里就够了。

### 两套主题的对照

| 令牌 | 浅色 | 深色 | 作用 |
|---|---|---|---|
| `--glass-base` | `.44` | `.085` | 玻璃的白底浓度。**这是两套主题唯一的材质差异** |
| `--glass-bright` | `1.02` | `.94` | 背景提亮/压暗。苹果的浅色材质提亮，深色材质压暗 |
| `--glass-stroke` | `rgba(255,255,255,.92)` | `rgba(255,255,255,.13)` | 1px 主描边 |
| `--glass-edge` | `rgba(17,24,39,.055)` | `rgba(255,255,255,.05)` | 外圈 0.5px 发丝线 |
| `--glass-hi` | `rgba(255,255,255,.95)` | `rgba(255,255,255,.13)` | 顶部内高光 |
| `--ink` | `#0a1020` | `#f2f5fa` | 主文字 |
| `--ink-2` | `#3d465a` | `#aeb8ca` | 次级文字 |
| `--ink-3` | `#5d6779` | `#8590a3` | 辅助文字 |
| `--hairline` | `rgba(17,24,39,.09)` | `rgba(255,255,255,.10)` | 分隔线 |
| `--accent` | `#0a7cff` | `#0a84ff` | 苹果蓝 |
| `--shadow-2` | `0 14px 34px -16px rgba(17,24,39,.24)` | `0 16px 40px -20px rgba(0,0,0,.85)` | 卡片投影 |

共用（不随主题变，参数面板调的就是这几个）：

| 令牌 | 默认 | 说明 |
|---|---|---|
| `--glass-blur` | `24px` | 模糊半径 |
| `--glass-sat` | `180%` | 背景饱和度倍数。苹果材质的签名参数 |
| `--glass-tint` | `1` | 浓度倍率，乘在 `--glass-base` 上 |
| `--grain` | `.022` | 噪点，只为了压渐变色带 |

### 字节（字体与尺度）

```
字体栈  -apple-system → SF Pro → Segoe UI Variable → PingFang SC → 系统字体
        （在 Mac 上是纯正 SF，在 Windows 上是 Segoe UI Variable，都不加载网络字体）
正文     16px / 1.65
标题     clamp(2.35rem, 5.5vw, 4.15rem)，字重 600，字距 -.032em
圆角     面板 20px / 大面板 30px / 内部 13px
间距     基数 8px；区块 clamp(56px, 6.6vw, 92px)
缓动     --ease cubic-bezier(.32,.72,0,1)（通用）
        --ease-out cubic-bezier(.16,1,.3,1)（进场）
        --ease-inout cubic-bezier(.65,0,.35,1)（主题擦除）
```

字距是苹果味道里最容易被忽略的一项：大标题必须收紧（`-.03em` 左右），正文几乎不收（`-.006em`）。

---

## 结构分层

```
4  内容层      nav / main / footer
3  玻璃层      .glass —— backdrop-filter 在这里生效，靠第 1 层取色
2  噪点层      .grain  opacity .022  mix-blend-mode: overlay
1  壁纸层      .wall   background: var(--wall)
```

### 壁纸（这一层决定成败）

四到五个大半径 radial-gradient 均匀铺满整个视口，**颜色必须压到面板背后**，不能只堆在角落：

```css
--wall-light:
  radial-gradient(58% 48% at 22% 20%, rgba(176,205,253,.82) 0%, rgba(176,205,253,0) 62%),
  radial-gradient(56% 46% at 78% 26%, rgba(208,189,252,.82) 0%, rgba(208,189,252,0) 62%),
  radial-gradient(54% 46% at 88% 80%, rgba(255,208,176,.78) 0%, rgba(255,208,176,0) 64%),
  radial-gradient(56% 48% at 14% 84%, rgba(176,240,226,.78) 0%, rgba(176,240,226,0) 64%),
  linear-gradient(168deg,#f6f9ff 0%,#ebf1fc 48%,#f7f2fd 100%);
```

四个色块分别落在四分之一画面，保证**任何位置下面都有颜色**。只放两个角落的结果是：中间的面板全是白的，看起来像卡片而不是玻璃。

> 两套壁纸定义在 `:root` 里，名字是 `--wall-light` / `--wall-dark`，再由各主题映射成 `--wall`。这样切换幕布才能读到「目标主题」的那一套。

---

## 核心技术点

### 1. 玻璃公式

```css
.glass{
  position:relative;
  background:linear-gradient(
    180deg,
    rgba(255,255,255,calc(var(--glass-base) * var(--glass-tint) + .05)),
    rgba(255,255,255,calc(var(--glass-base) * var(--glass-tint)))
  );
  -webkit-backdrop-filter:blur(var(--glass-blur)) saturate(var(--glass-sat)) brightness(var(--glass-bright));
  backdrop-filter:blur(var(--glass-blur)) saturate(var(--glass-sat)) brightness(var(--glass-bright));
  border:1px solid var(--glass-stroke);
  border-radius:var(--radius);
  box-shadow:
    inset 0 1px 0 var(--glass-hi),        /* 上边缘受光 */
    0 0 0 .5px var(--glass-edge),          /* 外圈发丝线 */
    var(--shadow-1),
    var(--shadow-2);
}
```

五个要点：

1. **`saturate(180%)` 是灵魂**。模糊会把背景颜色洗淡，必须补饱和度才能保住色相。少了它，玻璃就是一层灰雾。
2. **`brightness()` 分主题**：浅色 `1.02` 提亮，深色 `.94` 压暗。苹果的材质在两种模式下对背景的处理方向是相反的。
3. **背景用线性渐变而不是纯色**，`.05` 的落差让表面有轻微的斜向亮度。
4. **发丝线用 `0 0 0 .5px`** 而不是 `border`。`.5px` 在高分屏上是真正的半像素线，视觉上比 1px 轻一半——这是「精致」和「土」的分界。
5. **投影半径大、扩散负、透明度低**。`0 14px 34px -16px rgba(17,24,39,.24)`；小半径黑影是 Material 语言。

### 2. 主题切换：View Transitions（首选）

这是这套页面里技术含量最高、也最容易被做坏的部分。

**错误做法**：盖一张纯色/壁纸层的幕布扫过去。问题在于铺满的那一侧只有壁纸，**页面内容会消失**，用户看到的是「内容 → 空白 → 内容」。

**正确做法**：让浏览器把切换前/切换后各拍一张快照，叠加后给新快照加遮罩，擦出来的是真实内容。

```js
var vt = document.startViewTransition(function () { commit(t); });
```

```css
/* 每个伪元素单独一条规则：选择器列表里只要有一个不被识别，整条规则都会被丢弃 */
::view-transition-group(root){ animation:none; }
::view-transition-image-pair(root){ animation:none; isolation:auto; }
::view-transition-old(root){
  animation:none;   /* 关掉 UA 默认的淡入淡出，旧页面必须一直铺在下面 */
  z-index:1;
}
::view-transition-new(root){
  z-index:2;
  --sweep:0;
  mask-image:linear-gradient(135deg,
    #000 0,
    #000 calc(var(--sweep) * 125% - 25%),
    transparent calc(var(--sweep) * 125%));
  animation:vt-sweep 760ms var(--ease-inout) forwards;
}
@keyframes vt-sweep{ from{--sweep:0} to{--sweep:1} }
```

**遮罩的算术**（值得单独理解，改方向/改羽化程度都靠它）：

| `--sweep` | 黑色止点 | 透明止点 | 效果 |
|---|---|---|---|
| `0` | `-25%` → 钳到 0 | `0%` | 完全透明，什么都没擦出来 |
| `0.5` | `37.5%` | `62.5%` | 擦到一半，边缘有 25% 的羽化带 |
| `1` | `100%` | `125%` | 完全黑 → 新页面全覆盖 |

`125% - 25%` 这两个数是为了让**羽化带宽度恒定（25%）**，同时保证 `sweep=1` 时黑色能真正覆盖到 100%。如果只写 `calc(var(--sweep)*100%)`，铺满那一刻最后一个角还是半透明的，会露出一条旧页面。

**方向**：`135deg` 从左上指向右下。改成 `315deg` 就是右下→左上；`45deg` 是左下→右上。

### 3. `@property`：让自定义属性可插值

```css
@property --sweep {
  syntax: "<number>";
  inherits: true;
  initial-value: 0;
}
```

三件事必须知道：

- 不加 `@property`，`--sweep` 就是个字符串，`transition` / `@keyframes` 都无法插值，动画会直接跳变。
- `inherits: true` 是必须的。幕布上的斜光要读到同一个进度（`opacity:clamp(0, calc(1.7 - var(--sweep)*1.9), 1)`），`::after` 只能靠继承拿到它。
- 不支持 `@property` 的浏览器里 `--sweep` 会从 0 直接跳到 1——结果是「瞬间切换」，仍然可用，只是没有过渡。

### 4. 首屏防闪（FOUC）

主题必须在**第一次绘制之前**定下来，否则深色用户会看到一帧白屏。

```html
<script>
(function () {
  var saved = null;
  try { saved = localStorage.getItem('style_1.theme'); } catch (e) {}
  var sys = window.matchMedia('(prefers-color-scheme: dark)').matches ? 'dark' : 'light';
  var t = (saved === 'light' || saved === 'dark') ? saved : sys;
  document.documentElement.setAttribute('data-theme', t);
  document.documentElement.style.colorScheme = t;
})();
</script>
```

这段必须放在 `<head>` 里、样式表之前，且不能包在 `DOMContentLoaded` 里。

`colorScheme` 是给浏览器自己看的：滚动条、表单控件、系统下拉框会跟着变深变浅。

### 5. 换主题那一帧关掉所有过渡

```js
root.classList.add('no-anim');
root.setAttribute('data-theme', t);
void root.offsetWidth;                      // 强制一次样式计算
requestAnimationFrame(() => root.classList.remove('no-anim'));
```

```css
html.no-anim *, html.no-anim *::before, html.no-anim *::after{ transition:none !important; }
```

不加这一步，颜色会在切换后继续「爬」400ms，而幕布/快照早就不在了，用户会看到颜色在慢慢渗。

### 6. 主题切换控件

两段式 + 滑块拇指，位置由 `html[data-theme]` 驱动，**不需要 JS 去挪**：

```css
.ts-thumb{ position:absolute; top:3px; left:3px; width:36px; height:30px; transition:transform .42s var(--ease-out); }
html[data-theme="dark"] .ts-thumb{ transform:translateX(36px); }
```

无障碍上用 `role="radiogroup"` + `role="radio"` + `aria-checked`，而不是 `aria-pressed`：这是单选，不是开关。

### 7. 其它

- **入场**：`.js .reveal{opacity:0}` —— 隐藏必须挂在 `.js` 下，否则禁用 JS 时整页空白。
- **卡片倾斜**：≤ 3.2°。苹果的手感是「几乎察觉不到」，超过 5° 立刻廉价。
- **缩略图强度**：`--thumb-strength` 浅色 `.55` / 深色 `.92`。同一组渐变色，浅色主题下必须压下去，否则会变成全页最吵的东西。
- **本地光晕**：技术栈面板背后没有彩色时，玻璃会退化成白板，需要给它一层 `::before` 的 `radial-gradient` + `blur(30px)`。

---

## 动效清单

| 元素 | 触发 | 时长 | 曲线 | 变化 |
|---|---|---|---|---|
| 主题擦除 | 点击切换 | 760ms | `cubic-bezier(.65,0,.35,1)` | 新页面从左上角斜向擦出，羽化带 25% |
| 主题拇指 | 切换 | 420ms | `--ease-out` | `translateX(36px)` |
| 卡片入场 | 进入视口 | 800ms | `--ease-out` | 上移 18px + 淡入，错峰 60ms |
| 卡片悬停 | pointerenter | 450ms | `--ease-out` | 上移 4px、倾斜 ≤3.2°、投影加深 |
| 缩略图 | 卡片悬停 | 800ms | `--ease-out` | 背景色块放大 1.08、玻璃片上浮 4px |
| 按钮 | hover / active | 250ms | `--ease` | 上移 2px / 按下 `scale(.985)` |
| 导航 | 滚动 > 20px | 500ms | `--ease` | 上下内边距收窄 |
| 跑马灯 | 常驻 | 46s | `linear` | `translate3d(-50%,0,0)`，悬停暂停 |
| 状态点 | 常驻 | 2.6s | `--ease` | 绿色脉冲 |

---

## 降级与可访问性

| 情况 | 处理 |
|---|---|
| 不支持 View Transitions | 退回斜向幕布：铺目标主题的壁纸，铺满时换主题再撤幕布。效果略弱（过程中内容被壁纸盖住），但方向一致 |
| 不支持 `@property` | `--sweep` 跳变 → 瞬间切换，内容仍然正确 |
| 不支持 `backdrop-filter` | `@supports not (...)` → 浅色 `rgba(255,255,255,.92)`、深色 `rgba(18,24,38,.92)` 的不透明表面 |
| `prefers-reduced-motion: reduce` | 不做擦除，直接切主题；入场动画关闭；跑马灯停止 |
| 禁用 JS | 首屏按系统外观渲染（内联脚本若也被禁用则默认浅色），所有 `.reveal` 内容可见，切换控件不可用但页面完整 |
| 键盘 | 首个可聚焦元素是「跳到正文」；`aria-checked` 随状态更新；`Esc` 关参数面板 |
| 读屏 | 装饰层（壁纸/噪点/缩略图）`aria-hidden`；复制反馈 `role="status" aria-live="polite"`；主题控件 `role="radio"` + `aria-label` |
| 对比度 | 正文 `--ink` 在玻璃上 ≥ 12:1；`--ink-3` 辅助文字 ≥ 4.6:1，均过 WCAG AA |
| 系统外观变化 | 用户没手动选过时自动跟随（`matchMedia` 监听） |

---

## 常见错误

按「做出来为什么不对劲」排序：

1. **壁纸只在角落有颜色。** 中间的面板就变成白卡。四五个色块要铺满整个视口。
2. **只用 `blur`，不加 `saturate`。** 模糊把颜色洗淡 → 灰雾。苹果的材质靠 `saturate(180%)` 把颜色拉回来。
3. **玻璃太白（alpha > .7）。** 浅色主题尤其容易做成「一张白卡片」。`.44` 左右才透得出来。
4. **整页只有一种主题的壁纸。** 深色主题直接用 `#000` 或 `#0d1117` 当底，玻璃没有颜色可透，只能靠白描边撑场面。
5. **用纯色幕布做主题切换。** 铺满那一刻内容消失，看起来像闪了一下。要用快照（View Transitions）或直接把主题换在一帧里。
6. **`@property` 忘了 `inherits: true`。** 幕布上的斜光读不到进度，一直是最亮或最暗。
7. **遮罩止点没做到 100%。** `sweep=1` 时最后一个角还是半透明，撤幕布会闪一下。
8. **换主题时没关过渡。** 颜色在幕布消失之后继续爬 400ms。
9. **主题写在 `DOMContentLoaded` 里。** 深色用户会看到一帧白屏。
10. **四条边都加内高光。** 变成发光边框。只加上边缘。
11. **缩略图两种主题用同一强度。** 浅色下会变成全页最艳的东西。
12. **忘了 `-webkit-backdrop-filter`。** Safari 至今需要前缀。

---

## 怎么改

| 想改什么 | 动哪里 |
|---|---|
| 换主色 | `--accent` / `--accent-2`（两个主题块各改一次） |
| 换壁纸 | `:root` 里的 `--wall-light` / `--wall-dark`。改完记得看**面板背后**有没有颜色 |
| 玻璃更清 / 更实 | 只改 `--glass-base`（浅 `.35`–`.55`，深 `.06`–`.12`），别动 `blur` |
| 擦除方向 | `::view-transition-new(root)` 和 `.theme-wipe` 里的 `135deg`：`45deg` 左下→右上，`315deg` 右下→左上 |
| 擦除更快 | 改 `--wipe-dur`（默认 760ms，正文里 `WIPE_MS` 要同步） |
| 擦除边缘更软 | 遮罩里的 `125%` / `-25%` 间距，差值越大羽化带越宽 |
| 更安静 | 关掉跑马灯、卡片倾斜改 0、噪点提到 `.05` |
| 加一种新面板 | 直接复用 `.glass`；如果它背后是纯底色，记得补 `::before` 光晕 |

---

## 文件说明

```
src/style_1/
├── index.html   # 页面 + 内联样式 + 内联脚本（约 1500 行）
└── README.md    # 本文件
```

`index.html` 阅读顺序：

1. `<head>` 的内联主题脚本 —— 防闪，先看这个
2. `:root` —— 共用令牌 + 两套壁纸
3. `html[data-theme="light"]` / `["dark"]` —— 两套主题的差异
4. `/* ── 玻璃 */` —— 核心公式
5. `/* ── 主题切换 · 方案 A / B */` —— 擦除的两条路径
6. 末尾 `<script>` —— 主题、滚动、入场、倾斜、复制、参数面板

---

← 回到 [项目说明](../../README.md)
