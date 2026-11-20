# web_style

**一个前端设计风格的参考库。** 每种风格 = 一个能直接打开看的页面 + 一份讲清它为什么长这样的配方文档。

不做组件库，不做模板市场。这里只回答一个问题：*「我想要那种感觉」——那种感觉具体是哪几个 CSS 属性？*

---

## 目录结构

```
web_style/
├── README.md              # 你正在看的这个：项目说明 + 术语表 + 提示词模板
└── src/
    ├── style_1/           # 苹果式清晰毛玻璃（浅/深双主题 + 斜向擦除切换）
    │   ├── index.html     # 单文件页面，双击即可打开
    │   └── README.md      # 这一种风格的配方、技术点、反模式
    ├── style_2/           # 像素 / 16-bit 街机（白天·夜晚双配色 + 画布精灵 + 分帧刷屏）
    │   ├── index.html     # 同上：单文件，零网络请求
    │   ├── README.md
    │   └── OFL-Silkscreen.txt   # 内联像素字体的授权全文
    └── style_N/           # 之后新增的风格，同构
```

每个 `style_?` 目录都是**自包含**的：一个 HTML（样式内联，无构建、无依赖），一份 README（讲这一种风格）。

## 怎么用

| 你想做什么 | 怎么做 |
|---|---|
| 看效果 | 双击 `src/style_1/index.html` 或 `src/style_2/index.html` |
| 看深色 | 点右上角 ☀/☾，主题由左上角斜向擦到右下角（`style_1`） |
| 看两套配色 | 点右上角太阳/月亮，新画面分 18 条横向刷下来（`style_2`） |
| 抄材质 | 打开页面源码，搜 `/* ── 玻璃` 这一段 |
| 调参数 | 右下角齿轮「材质参数」：模糊 / 浓度 / 饱和度 / 噪点，实时生效 |
| 调像素风参数 | `style_2` 右下角 OPTIONS：阴影深度 / 扫描线 / 帧率 / 强调色 |
| 学配方 | 读对应目录的 `README.md` |
| 让 AI 生成同风格页面 | 用本文末尾的「提示词模板」，把 `{风格配方}` 换成对应 README 的令牌表 |

> 页面里的文字是**示例文案**，换项目时整体替换即可。两个样例的人名都用占位（`你的名字`），邮箱用 `hello@yourname.com`。

---

## 风格索引

| 目录 | 风格 | 关键词 | 适合 | 状态 |
|---|---|---|---|---|
| [`style_1`](src/style_1/) | 苹果式清晰毛玻璃 | `backdrop-filter` + `saturate(180%)`、发丝描边、内高光、双主题、View Transitions 斜向擦除 | 个人主页、产品落地页、SaaS 后台、设置面板 | ✅ 已完成 |
| [`style_2`](src/style_2/) | 像素 / 16-bit 街机 | 2px 描边 + 零模糊硬投影、有序抖动、画布精灵、`steps()` 量化动效、分帧刷屏换配色 | 个人主页、游戏/工具落地页、活动页、复古后台 | ✅ 已完成 |

---

## 前端术语速查

按「你在纠结什么」分组。每条给：英文术语 → 中文 → 一句话解释 → 关键属性。

### 1. 材质与质感（Material）

| 术语 | 中文 | 说明 | 关键属性 |
|---|---|---|---|
| **glassmorphism** | 毛玻璃 / 玻璃拟态 | 半透明表面 + 背景模糊 + 细边框 + 内高光，让 UI 像浮在背景上的一块玻璃 | `backdrop-filter` |
| **backdrop-filter** | 背景滤镜 | 对**元素背后**的内容做模糊/调色，元素自身内容不受影响 | `backdrop-filter: blur(22px) saturate(150%)` |
| **inner highlight** | 内高光 | 顶部一条 1px 白色内阴影，模拟玻璃边缘的受光面，是「有厚度」的关键 | `box-shadow: inset 0 1px 0 rgba(255,255,255,.16)` |
| **noise / grain** | 噪点 / 颗粒 | 一层极淡的随机噪声，消除大面积渐变的色带（banding），让表面有「粗糙度」 | `feTurbulence` + `mix-blend-mode: overlay` |
| **banding** | 色带 | 渐变过渡不够平滑时出现的同心色阶，深色背景上尤其明显 | 用噪点或 `dither` 掩盖 |
| **elevation** | 层级 / 投影高度 | 用阴影的方向和模糊半径表达「离背景多远」 | `box-shadow: 0 30px 60px -32px rgba(2,6,20,.92)` |
| **blend mode** | 混合模式 | 决定两个图层怎么叠加；`screen` 提亮、`overlay` 增强对比、`multiply` 压暗 | `mix-blend-mode` |
| **specular** | 高光 / 镜面反射 | 玻璃、金属表面上那块最亮的光斑，通常用 radial-gradient 画 | `radial-gradient(circle at 32% 26%, #fff, transparent 44%)` |
| **surface** | 表面 / 承载面 | 卡片、面板、抽屉这类「一块东西」的统称 | — |
| **vibrancy** | 活力 / 通透感 | 苹果对材质的说法：内容透过材质时不只被模糊，还被提亮、加饱和，所以「透」但不「脏」 | `saturate()` + `brightness()` |
| **saturation boost** | 饱和度补偿 | 模糊会把背景颜色洗淡，必须补回来；这是「清晰的毛玻璃」与「灰雾」的分界 | `backdrop-filter: blur(24px) saturate(180%)` |
| **material** | 材质 | 一整套表面规则（底色、模糊、描边、内高光、投影），不是单个属性 | — |
| **hairline border** | 发丝线 | 0.5px 级的描边/分隔线，在高分屏上才有意义；是「精致」的关键细节 | `box-shadow: 0 0 0 .5px rgba(0,0,0,.06)` |
| **continuous radius** | 连续圆角 | 苹果的圆角不是正圆弧，是曲率连续的超椭圆；CSS 没原生支持，但可以用「内层圆角 = 外层 − padding」逼近 | `border-radius: calc(var(--r) - 12px)` |
| **scrim** | 蒙层 | 压在图片/渐变上的一层半透明色，用来保证其上的文字可读 | `background:rgba(12,18,32,.42)` |

### 2. 布局（Layout）

| 术语 | 中文 | 说明 | 关键属性 |
|---|---|---|---|
| **grid / flexbox** | 网格 / 弹性盒 | 二维用 grid，一维（一行或一列）用 flex | `display: grid` / `flex` |
| **asymmetric grid** | 非对称栅格 | 列宽不等（7:5、8:4），比等分更编辑化、更有节奏 | `grid-template-columns: repeat(12,1fr)` + `span` |
| **auto-fit / minmax** | 自适应填充 | 卡片数未知时的响应式写法，自动决定几列 | `repeat(auto-fit, minmax(300px, 1fr))` |
| **intrinsic sizing** | 内在尺寸 | 让内容决定容器大小，而不是硬编码宽高 | `min-content`、`max-content`、`fit-content` |
| **container query** | 容器查询 | 按**父容器**宽度响应，而不是视口；组件真正可复用的前提 | `@container (min-width: 400px)` |
| **fluid layout** | 流式布局 | 尺寸随视口连续变化，中间没有断点跳变 | `clamp()`、`min()`、`vw` |
| **gutter** | 槽宽 | 栅格列与列之间的间隙 | `gap` |
| **sticky** | 粘性定位 | 滚动到阈值后吸附，常用于章节标题和导航 | `position: sticky; top: 0` |

### 3. 字体排版（Typography）

| 术语 | 中文 | 说明 | 关键属性 |
|---|---|---|---|
| **fluid type** | 流体字号 | 字号随视口线性变化，不用为每个断点写一套 | `font-size: clamp(2.3rem, 6vw, 4.6rem)` |
| **modular scale** | 模数音阶 | 用固定比例（1.25 / 1.5 / 1.618）生成整套字号，避免随手写 px | — |
| **display type** | 展示字体 | 大标题专用，允许更极端的字号和更紧的字距 | `letter-spacing: -.02em` |
| **tracking / letter-spacing** | 字距 | 大字号收紧、小字号（尤其全大写）放开 | `letter-spacing` |
| **leading / line-height** | 行高 | 正文 1.6–1.8，大标题 1.0–1.1 | `line-height` |
| **measure** | 行长 | 一行容纳的字符数；正文 45–75 字符最舒服 | `max-width: 46ch` |
| **text-wrap: balance** | 标题均衡断行 | 让多行标题各行长度接近，避免孤字 | `text-wrap: balance` |
| **text-wrap: pretty** | 正文优化断行 | 减少孤行（最后一行只剩一两个字） | `text-wrap: pretty` |
| **widow / orphan** | 孤行 / 寡行 | 段落首尾被甩到单独一行的字，排版上要避免 | — |
| **eyebrow** | 眉标 | 标题上方那行小字（全大写 + 宽字距），用来交代上下文 | `text-transform: uppercase; letter-spacing: .16em` |
| **tabular numbers** | 等宽数字 | 数字宽度一致，列表/表格/计时器不跳动 | `font-variant-numeric: tabular-nums` |
| **gradient text** | 渐变文字 | 把渐变裁进文字形状里 | `background-clip: text; color: transparent` |

### 4. 色彩（Color）

| 术语 | 中文 | 说明 | 关键属性 |
|---|---|---|---|
| **oklch** | 感知均匀色彩空间 | 同样 L 值的两个颜色看起来一样亮；做色阶和暗色模式比 HSL 靠谱 | `oklch(0.72 0.15 190)` |
| **design token** | 设计令牌 | 颜色/间距/圆角/动效的命名变量，改一处全站生效 | `--color-accent: #6ee7cf` |
| **accent** | 强调色 | 全站唯一的「动作色」，用于主按钮、链接、焦点 | — |
| **neutral ramp** | 中性色阶 | 带一点色相倾向的灰（冷灰/暖灰），比纯灰更高级 | — |
| **aurora / mesh gradient** | 极光 / 网格渐变 | 多个大半径模糊色块叠加出的有机渐变 | 多个 `radial-gradient` + `filter: blur()` |
| **color temperature** | 色温 | 冷色后退、暖色前进；冷暖搭配产生纵深 | — |
| **contrast ratio** | 对比度 | 正文 ≥ 4.5:1，大字 ≥ 3:1（WCAG AA） | — |
| **alpha layering** | 透明度叠层 | 用 `rgba(255,255,255,.06)` 而不是写死的深灰色，才能透出背景 | — |

### 5. 主题与外观（Theming）

| 术语 | 中文 | 说明 | 关键属性 |
|---|---|---|---|
| **theme token** | 主题令牌 | 同一套语义变量（`--ink` / `--surface` / `--accent`）在两套主题里各给一组值，结构不动只换皮 | `html[data-theme="dark"]{--ink:#f2f5fa}` |
| **prefers-color-scheme** | 系统外观偏好 | 读取用户系统是浅色还是深色，用于首屏默认值 | `matchMedia('(prefers-color-scheme: dark)')` |
| **color-scheme** | 声明式配色方案 | 告诉浏览器本页支持哪些模式，滚动条、表单控件、下拉框会跟着变 | `color-scheme: light dark` |
| **FOUC / FOIT** | 无样式/无字体内容闪现 | 样式或字体到位前先渲染了一帧错误的样子；主题场景下表现为「闪白」 | 内联脚本 + `<head>` 里同步执行 |
| **View Transitions** | 视图过渡 | 浏览器为「切换前/切换后」各拍一张快照并叠加，可对其做裁剪、遮罩、位移 | `document.startViewTransition()` |
| **`::view-transition-new(root)`** | 新页面快照伪元素 | 承载切换后画面的那一层，加遮罩就等于「把新页面擦出来」 | `mask-image` / `clip-path` |
| **`@property`** | 可插值的自定义属性 | 给自定义属性声明类型，它才能被 `transition` / `@keyframes` 平滑插值 | `@property --sweep { syntax:"<number>"; inherits:true; initial-value:0 }` |
| **mask wipe** | 遮罩擦除 | 用一条渐变遮罩推进来「擦」出画面，边缘天然带羽化，比 `clip-path` 的硬边柔和 | `mask-image: linear-gradient(135deg, ...)` |
| **theme persistence** | 主题记忆 | 把用户选择写进 `localStorage`，优先于系统偏好 | `localStorage.setItem('theme', t)` |
| **no-anim frame** | 免过渡帧 | 换主题那一刻临时禁用所有过渡，否则颜色会在画面切完之后继续慢慢爬 | `html.no-anim *{transition:none !important}` |

### 6. 动效（Motion）

| 术语 | 中文 | 说明 | 关键属性 |
|---|---|---|---|
| **easing** | 缓动曲线 | 决定「加速-减速」的手感；`cubic-bezier(.22,.61,.36,1)` 是最通用的「舒服」 | `transition-timing-function` |
| **ease-out** | 出场缓动 | 起步快、收尾慢，界面元素进场首选 | `cubic-bezier(.16,1,.3,1)` |
| **duration scale** | 时长刻度 | 微交互 120–200ms，面板 240–320ms，页面转场 400–600ms | — |
| **micro-interaction** | 微交互 | 悬停、按下、聚焦这类毫秒级反馈，决定「精不精致」 | — |
| **stagger** | 错峰 / 依次入场 | 列表元素逐个延迟出现，避免一起蹦出来 | `transition-delay: calc(var(--i) * 70ms)` |
| **scroll reveal** | 滚动入场 | 元素进入视口时淡入上移 | `IntersectionObserver` |
| **cursor spotlight** | 光标聚光 | 光斑跟随指针，靠 CSS 变量把坐标传进 gradient | `--mx` / `--my` + `radial-gradient` |
| **tilt / parallax** | 倾斜 / 视差 | 鼠标位置驱动轻微 3D 旋转，制造「卡片是实体」的错觉 | `perspective()` + `rotateX/Y` |
| **marquee** | 跑马灯 | 无缝横向滚动：内容复制一份，位移 -50% 后重置 | `@keyframes` + `translate3d(-50%,0,0)` |
| **reduced motion** | 减少动效偏好 | 尊重系统的「减少动态效果」设置，关闭位移类动画 | `@media (prefers-reduced-motion: reduce)` |
| **will-change** | 提前提升图层 | 告诉浏览器该元素将变化，减少掉帧；滥用会吃内存 | `will-change: transform` |
| **custom property animation** | 自定义属性动画 | 把进度存进 `--p` 再让别的属性读它，一个变量可以同时驱动遮罩、位移、透明度 | `@keyframes k{to{--p:1}}` |
| **theme wipe** | 主题擦除 | 换肤时用一条斜向推进的遮罩把新画面擦出来，比整体淡入更有物理感 | `mask-image` + `@property` |
| **cross-fade** | 交叉淡化 | 新旧画面同时改变透明度；简单但容易「发灰」，也遮盖不了内容替换的断层 | `opacity` 对偶 |

### 7. 工程与性能（Engineering）

| 术语 | 中文 | 说明 | 关键属性 |
|---|---|---|---|
| **progressive enhancement** | 渐进增强 | 先保证基础可用，再用新特性加分；`@supports` 是它的语法 | `@supports (backdrop-filter: blur(1px))` |
| **graceful degradation** | 优雅降级 | 特性不支持时给出可接受的替代（如不透明背景） | — |
| **repaint / reflow** | 重绘 / 重排 | 改 `transform`、`opacity` 只重绘；改 `width`、`top` 会重排，更贵 | — |
| **compositing layer** | 合成层 | 独立于主线程合成的图层；模糊、变换、`will-change` 会触发 | — |
| **critical path** | 关键渲染路径 | 从 HTML 到首屏像素的必经步骤，越短越快 | — |
| **self-contained file** | 自包含单文件 | 样式脚本内联，双击即开，适合做参考样本 | — |
| **semantic HTML** | 语义化标签 | `nav / main / section / article / footer` 让结构自解释 | — |
| **focus-visible** | 键盘焦点态 | 只在键盘操作时显示焦点环，鼠标点击不显示 | `:focus-visible` |

### 8. 可访问性（Accessibility）

| 术语 | 中文 | 说明 |
|---|---|---|
| **a11y** | 无障碍（accessibility 的缩写） | 让键盘、读屏、低视力用户都能用 |
| **skip link** | 跳过导航链接 | 页面第一个可聚焦元素，让键盘用户直跳正文 |
| **aria-live** | 动态区域播报 | 内容异步变化时通知读屏软件（如「已复制」提示） |
| **touch target** | 触摸目标 | 可点区域 ≥ 44×44px，手指才不容易点错 |
| **decorative element** | 装饰性元素 | 纯装饰要加 `aria-hidden="true"`，别让读屏念出来 |

### 9. 像素与低分辨率美学（Pixel & Low-Res）

`style_2` 用到的词。这一族的共同点：**所有「连续」都要换成「离散」**。

| 术语 | 中文 | 说明 | 关键属性 |
|---|---|---|---|
| **pixel-perfect** | 整像素对齐 | 每条边都落在整数设备像素上，不出现半像素的灰边 | 整数 px + `Math.round(devicePixelRatio)` |
| **integer scaling** | 整数倍缩放 | 精灵只按 2×/3×/4× 放大，像素大小才一致 | `width: 16px * n` |
| **nearest neighbour** | 最近邻采样 | 放大时直接复制最近的像素，不插值出中间色 | `image-rendering: pixelated` |
| **dithering / ordered dither** | 抖动 / 有序抖动 | 用网点的疏密假装出更多色阶（Bayer 矩阵是最经典的一种） | `repeating-conic-gradient(...) 0 0/4px 4px` |
| **limited palette** | 受限调色板 | 固定 4–16 个颜色；颜色越少，风格越不容易散 | CSS 变量 |
| **sprite / sprite sheet** | 精灵 / 精灵表 | 逐像素画的小图标，按整数倍显示 | `<canvas>` + `fillRect` |
| **indexed colour** | 索引色 | 每个像素只记调色板下标，不记 RGB —— 所以能写成字符串 | 字符矩阵 `'..kk....'` |
| **bevel** | 斜面 | 左上受光、右下背光的 2px 内描边，制造「凸起」 | 两条 `inset` box-shadow |
| **hard shadow** | 硬投影 | 零模糊、带偏移的实色影子 | `box-shadow: 6px 6px 0` |
| **stat bar / segmented meter** | 格状状态条 | 把百分比画成 N 个小方块，而不是一条连续长度 | `nth-child(-n+4)` |
| **steps()** | 阶梯缓动 | 动画只走固定几帧，不插值 | `steps(4, end)` |
| **frame quantisation** | 帧率量化 | 把动效锁在 12 / 24 / 60fps，得到「机器感」的顿挫 | `steps()` + 时长一起换 |
| **scanline** | 扫描线 | 每 3px 一条 1px 暗线，模拟 CRT 行扫描 | `repeating-linear-gradient(0deg, …)` |
| **blocky wipe** | 分条刷屏 | 用 `clip-path` + `steps()` 把新画面一条条刷出来，而不是淡入 | `clip-path: inset()` |
| **crisp edges** | 锐边 | SVG 的 `shape-rendering:crispEdges`；位图的 `image-rendering:pixelated` | — |
| **1px vs 2px border** | 单像素 / 双像素描边 | 4px 以上显笨；1px 在深色底上会「漏光」；2px 最稳 | `border: 2px` |

---

## 提示词模板

给 AI（或给自己）描述风格时，**说清「材质 + 结构 + 动效」比说「高级感」有用一百倍**。下面这套结构可以直接复用。

### 通用结构

```
【产物】{页面类型}，{尺寸/视口}，{单文件 / 框架}
【受众】{谁看}，{在什么距离看：手机 / 笔记本 / 投影}
【风格】{风格名}，参考 {品牌或设计师}
【材质】{表面处理：玻璃 / 纸感 / 金属 / 无材质纯排版}
【色彩】底色 {hex}，主色 {hex}，强调色 {hex}，{冷/暖}倾向
【字体】标题 {字体名}，正文 {字体名}，字号策略 {clamp 区间}
【布局】{栅格策略}，间距基数 {8px}，圆角 {大/小/锐利}
【动效】{时长}，{曲线}，{触发方式}
【必须包含】{具体元素清单}
【明确不要】{反模式清单}
```

### 可复制版本（中文）

> 做一个 **{个人主页}**，{1440 桌面 + 390 移动}自适应，{单文件 HTML + 内联 CSS}。
> 受众是{设计同行}，主要在{笔记本}上看。
> 风格走 **{毛玻璃 + 极光渐变}**，参考 {macOS 控制中心} 的材质感。
> 材质：半透明玻璃表面（`backdrop-filter: blur(22px) saturate(150%)`），1px 白色 14% 描边，顶部一条 1px 内高光。
> 色彩：底色 `{#050810}`，主色 `{#6ee7cf}`，辅助 `{#8aa4ff}`，暖色点缀 `{#ffb783}`；三色之间要有温度差。
> 字体：标题用 {衬线体}（`clamp(2.35rem, 6vw, 4.6rem)`），正文用 {无衬线体} 16–17px，行高 1.7。
> 布局：{12 列栅格}，卡片用 {7:5 / 5:7} 非对称跨度，间距基数 {8px}，圆角 {22px}。
> 动效：{180–320ms}，`cubic-bezier(.22,.61,.36,1)`，悬停时卡片轻微倾斜 + 光标聚光 + 渐变描边亮起。
> 必须包含：固定玻璃导航、Hero（大标题 + 一句话 + 两个按钮）、四张作品卡、技术栈面板、随笔列表、联系面板、页脚。
> 明确不要：{紫粉渐变、emoji 当图标、左侧色条卡片、Inter/Roboto 当标题、假数据}。

### 示例：生成 `style_1` 这一类页面

> 做一张**苹果式清晰毛玻璃**的个人主页，浅色/深色双主题。
>
> **壁纸**：4 个铺满全视口的 radial-gradient 色块（浅色用淡蓝 / 淡紫 / 蜜桃 / 薄荷，深色用深蓝 / 深紫 / 酒红 / 青黑），保证任何面板背后都有颜色 —— 这是这类风格成立的前提。
>
> **玻璃**：`rgba(255,255,255,.44)`（浅）/ `.085`（深）底色 + `backdrop-filter: blur(24px) saturate(180%) brightness(1.02 | .94)` + `rgba(255,255,255,.92 | .13)` 主描边 + `inset 0 1px 0` 内高光 + `0 0 0 .5px` 发丝线 + 大半径负扩散投影。
>
> **主题切换**：用 View Transitions，`::view-transition-new(root)` 加一条 135° 的羽化遮罩（`@property` 注册 `--sweep` 从 0 到 1），让新页面从左上角斜向擦到右下角；旧页面用 `::view-transition-old(root){animation:none; z-index:1}` 一直铺在下面。主题偏好存 localStorage，并在 `<head>` 内联脚本里先定下来防闪。
>
> **控件**：两段式深浅切换（`role="radiogroup"`），滑块由 `html[data-theme]` 属性驱动位移。
>
> **重点**：换主题那一帧关掉所有过渡；不支持 View Transitions 时退回斜向幕布；`prefers-reduced-motion` 下直接切换。

### 示例：生成 `style_2` 这一类页面

> 做一张**像素 / 16-bit 街机**的个人主页，白天（羊皮纸）与夜晚（靛蓝夜空）两套**配色**。
>
> **材质**：2px 墨线描边 + 两条 `inset` 内斜面（左上受光 `--edge-hi`、右下背光 `--edge`）+ `6px 6px 0` 零模糊硬投影。全站圆角为 0；描边只有 2px 和 1px 两种。
>
> **天空**：四段 `linear-gradient` 色阶，上面叠一层 4px 的 `repeating-conic-gradient` 棋盘格（25%/50% 两个止点 = 有序抖动）。底部一条用 `<canvas>` 画的地平线：**4 CSS px = 1 画布像素**，山脊高度量化到 8px 台阶，夜里再加 7 颗十字星。
>
> **字体**：Silkscreen（OFL）以 base64 内联，只给拉丁字母和数字用，字号必须是整数；中文交给系统黑体。理由：5×5 的点阵画不出汉字，硬造只会两头不讨好。
>
> **精灵**：图标、吉祥物、缩略图都是 16×16 / 8×8 的字符矩阵（`k` 轮廓、`f` 毛、`e` 眼窝……），运行时 `fillRect` 逐格画进 `<canvas>`。缩放只允许整数倍，`devicePixelRatio` 必须取整，否则在 Windows 的 1.5 倍屏上全糊。
>
> **动效**：全站只用 `steps()`，不用 `cubic-bezier`。三档：`steps(3)` 状态、`steps(4)` 组件、`steps(8)` 版块。换配色用 View Transitions 快照 + `clip-path: inset()` 配 `steps(18)`，新画面像 CRT 行同步一样一条条刷下来。
>
> **可选参数**：右下角 OPTIONS 提供阴影深度（0/4/6/8）、扫描线（0/.055/.14）、帧率（12/24/60）、强调色（琥珀/薄荷/玫红），全部写 localStorage。
>
> **重点**：换肤那一帧关掉过渡；`prefers-reduced-motion` 下不刷屏、不眨眼、停跑马灯；跑马灯按「一跳 8px」算 `steps` 数，别用百分比位移。

### 负向提示词（反向约束）

```
不要：紫→粉→蓝的默认科技渐变 / emoji 当图标 / 圆角卡片配左侧色条 /
Inter、Roboto、Arial 当展示字体 / CSS 画的假人物和假产品图 /
写死的假数据 / 大面积纯黑 #000 / 玻璃上叠纯白 8% 却没有背景可透 /
只用 blur 不加 saturate / 用纯色幕布做主题切换（铺满那刻内容会消失）/
主题写在 DOMContentLoaded 里（深色用户会闪一帧白）/ 换主题时还留着过渡
```

像素风另外几条：

```
不要：任何圆角 / 带模糊半径的投影 / 非整数倍放大的精灵 / 小数号的像素字体 /
用像素字体排中文正文 / 动效里混 cubic-bezier / 超过 5 个颜色 /
网点压在文字背后 / 扫描线浓度 > .14 / 用 emoji 当图标（这里用画布画的精灵）
```

### 迭代提示词（改稿时怎么说）

| 你想要的效果 | 这样说 |
|---|---|
| 玻璃更明显 | 「玻璃浓度 `.44` → `.52`，模糊 24px → 30px」 |
| 玻璃更清透 | 「底色 alpha `.44` → `.38`，饱和度 180% → 210%」 |
| 更安静 | 「去掉一半动效，只留悬停和入场；噪点从 .022 提到 .05」 |
| 更有层次 | 「给面板背后加局部光晕，让 `backdrop-filter` 有东西可以模糊」 |
| 更高级 | 「加一条 `0 0 0 .5px` 的发丝线，投影改成 `0 14px 34px -16px`」 |
| 切换更快/更慢 | 「`--wipe-dur` 760ms → 1000ms」（正文里的 `WIPE_MS` 要同步） |
| 换擦除方向 | 「遮罩角度 135° → 315°，从右下角擦起」 |
| 更聚焦 | 「删掉第二个 CTA 和页脚多余链接，一屏只留一个动作」 |
| 像素更「厚」 | 「`--depth` 6px → 8px，同时把描边确认还是 2px」 |
| 像素更「顿」 | 「OPTIONS 里帧率切 12fps」＝ `--dur-1` 240ms、`--f-1` `steps(2)` |
| 更像 CRT | 「`--scan` .055 → .14，跑马灯每跳 8px → 4px」 |
| 换个时代 | 「`--accent` 换薄荷，白天主题的 `--accent` 必须同步压深」 |
| 换吉祥物 | 「改 `SPR.cat` 的 16 行字符串，眼窝标记 `'e'`，瞳孔逻辑会自动适配」 |

---

## 新增一种风格

1. 建目录 `src/style_N/`
2. 放 `index.html`：单文件、样式内联、无构建依赖、双击可开
3. 放 `README.md`：至少包含 **一句话定义 / 视觉配方（令牌表）/ 核心技术点（带片段）/ 反模式 / 适合与不适合**
4. 回到本文件「风格索引」表里加一行

### 每个 `style_?/README.md` 的建议结构

```
# style_N · 风格名
一句话定义 + 适合什么 / 不适合什么
## 视觉配方      ← 色板、字体、间距、圆角、阴影、动效，全部写具体值
## 结构分层      ← 页面由哪几层叠出来
## 核心技术点    ← 每条一个可复制的 CSS 片段
## 动效清单      ← 什么元素、什么时候、多长、什么曲线
## 降级与可访问性
## 常见错误      ← 这种风格最容易被做坏的地方
## 怎么改        ← 换色/换字体/换密度时动哪几个变量
```

---

## 约定

- 不引入构建工具。参考样本要能双击打开。
- 每个页面自带 `prefers-reduced-motion` 降级。
- 做双主题的页面，主题必须在 `<head>` 的内联脚本里先定下来，不能等 `DOMContentLoaded`。
- 示例文案可以是假的，但**不要伪造数据**（客户名、指标、logo 墙）。
- 人名、邮箱一律用占位（`你的名字`、`hello@yourname.com`）。

## License

[MIT](LICENSE)
