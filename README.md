# 主站模板

一个零构建的 **静态主站 / Hub 模板**：开箱即用、深浅色自适应、移动端友好、带多语言切换（中/英/日）、卡片式项目导航、滚动分屏与入场动效、子集化中文字体与按需背景图加载。适合聚合你的实验分站、工具与博客入口。

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](./LICENSE)

---

## ✨ 特性一览

* **零依赖 / 零构建**
  纯 `index.html` + 本地资源，无需 Node、打包、框架或后端。支持任意静态托管（Vercel / GitHub Pages / Nginx / Caddy）。

* **深浅色自适应 + 背景智能选图**
  根据 `prefers-color-scheme` 与设备 DPR/分辨率，自动选择 **Light/Dark** 与 **1280/1920/2560/3840** 四档背景图，既清晰又省流量。窗口尺寸变化时轻量防抖重算。

* **子集化中文字体（本地托管）**
  `fonts/` 中已做按 Unicode Range 拆分的 `.woff2` 子集，`font-display: swap`，中文渲染稳且快，隐私友好（无需第三方 CDN）。

* **滚动分屏 & 入场动效**
  使用 `scroll-snap` 实现整屏滚动；IntersectionObserver 控制逐屏入场过渡。遵守无障碍偏好：若用户设置“**减少动态效果**”，则自动禁用动效。

* **多语言切换（中/英/日）**
  纯前端 i18n：在脚本内维护 `translations` 对象，通过 `data-i18n` / `data-i18n-aria-label` 自动替换文案与 aria 标签；带语言菜单与可见性逻辑。

* **玻璃拟态卡片 / 暗色霓虹渐变**
  CSS 变量 + `backdrop-filter` + `color-mix`，形成轻盈的玻璃卡片；可按变量统一换肤。

* **可访问性 & 语义结构**
  使用 `aria-` 属性（如按钮提示、菜单状态），兼顾键盘与读屏；语义化的 section/heading 结构。

* **极简 SEO**
  内置 `<meta name="description">`；可按需扩展 Open Graph、Twitter Cards。

---

## 🗂 目录结构

```
.
├─ index.html            # 主页面（含样式、脚本、卡片布局、i18n）
├─ assets/
│  ├─ background/        # 背景图（Light/Dark × 1280/1920/2560/3840）
│  └─ favicon.svg        # 站点图标（另有 favicon1.svg 备选）
└─ fonts/
   ├─ style.css          # 汇总引入各子集字体
   ├─ *.css              # 单字重子集（含 unicode-range）
   └─ files/*.woff2     # 字体子集文件
```

> 说明：当前 `index.html` 内引用资源使用 **绝对路径**（如 `/assets/...`、`/fonts/style.css`）。在**子路径**托管（如 `https://user.github.io/repo/`）时需改为相对路径或做发布前替换，下文提供 GitHub Actions 的自动重写方案。

---

## 🔧 使用说明

### 1) 本地预览（无需安装）

* 直接用浏览器打开 `index.html` 即可。
* 如需本地服务器（便于模拟路径等）：

  * Python：`python3 -m http.server 8787`
  * Node（无需项目依赖，仅全局）：`npx serve .` 或 `npx http-server -p 8787`

### 2) 自定义内容

* **站点卡片**
  在 `index.html` 的 “实验分站一览” `<section>` 中，每个卡片是一个 `<article class="card">`。
  修改：

  * 标题 `<h3 class="title">`
  * 描述 `<p class="text" data-i18n="...">`
  * 链接 `<a class="go" href="...">`
  * 标签 `<span class="tag" ...>`

* **文案与多语言**
  在 `index.html` 末尾第二段 `<script>` 的 `translations` 对象里维护多语言文案。

  * 在元素上加 `data-i18n="键名"`，脚本会自动注入文案。
  * 对 ARIA 文案，用 `data-i18n-aria-label="键名"`。
  * 要新增语言：

    1. 在 `languages=['zh','en','ja']` 中加入语言码（例如 `'fr'`）；
    2. 在 `translations.fr = {...}` 中提供同名键值；
    3. 在菜单里添加 `<button data-language-option="fr">Français</button>`。

* **深/浅色背景图**
  把新图放到 `assets/background/`，命名遵循：

  ```
  Light-1280x1280-hq.jpg
  Light-1920x1920-hq.jpg
  Light-2560x2560-hq.jpg
  Light-3840x3840-hq.jpg
  Dark-1280x1280-hq.jpg
  ...
  ```

  JS 会自动按 DPR/窗口挑选最合适的一张。你也可以改 `sizes=[1280,1920,2560,3840]` 或 `basePath`。

* **站点图标**
  替换 `assets/favicon.svg`；`<link rel="icon" href="/assets/favicon.svg">` 可按需改为 `.png`。

* **主题与样式**
  常用颜色、模糊强度、圆角、阴影、栅格间距均通过 `:root { --var }` 定义，集中修改即可。

* **SEO**
  补充以下 `<meta>` ：

  ```html
  <meta property="og:title" content="你的主站标题" />
  <meta property="og:description" content="一句话简介" />
  <meta property="og:type" content="website" />
  <meta property="og:image" content="/assets/og-cover.png" />
  <meta name="twitter:card" content="summary_large_image" />
  ```

  若部署在子路径，`/assets/...` 请考虑改成相对或绝对域名。

---

## ▲ 首选部署：Vercel

### 方案 A：Vercel 面板一键部署

一键部署：[![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https%3A%2F%2Fgithub.com%2Flzray-universe%2Fmain-website&project-name=main-website&repository-name=main-website&demo-url=https%3A%2F%2Flzray.com)

1. 将仓库推到 GitHub / GitLab。
2. 打开 [Vercel](https://vercel.com/) → **Add New… → Project** → 选择你的仓库。
3. 配置：

   * **Framework Preset**：`Other`
   * **Build Command**：留空（无构建）
   * **Output Directory**：`/`（根目录）
4. 点击 **Deploy** 完成上线。
5. （可选）绑定自定义域名，Vercel 会自动签发 HTTPS。

### 方案 B：`vercel.json`

在仓库根目录新增 `vercel.json`，优化静态缓存与 URL：

```json
{
  "cleanUrls": true,
  "trailingSlash": false,
  "headers": [
    {
      "source": "/assets/(.*)\\.(jpg|jpeg|png|svg|webp)",
      "headers": [{ "key": "Cache-Control", "value": "public, max-age=31536000, immutable" }]
    },
    {
      "source": "/fonts/(.*)\\.(woff2|woff)",
      "headers": [{ "key": "Cache-Control", "value": "public, max-age=31536000, immutable" }]
    },
    {
      "source": "/index.html",
      "headers": [{ "key": "Cache-Control", "value": "no-cache" }]
    }
  ]
}
```

### 方案 C：Vercel CLI（无需构建）

```bash
npm i -g vercel
vercel --prod
```

---

## 备选部署：GitHub Actions（部署到 GitHub Pages）

> **重要：路径问题**
> 本模板资源默认以 **绝对路径**引用（`/assets`、`/fonts`…）。
>
> * 如果你的 Pages 在根域（如 `https://user.github.io/` 或自定义根域），可直接发布。
> * 如果你的 Pages 在**项目子路径**（如 `https://user.github.io/repo/`），请使用 **“自动重写绝对路径为相对路径”** 的工作流（下方方案 B），否则资源会 404。

### 方案 A：部署到根域 / 已配置自定义根域

1. GitHub 仓库 → **Settings → Pages**：将 **Source** 设为 **GitHub Actions**。
2. 新建 `.github/workflows/pages.yml`：

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: [ main ]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      # 可选：列个目录看下产物
      - name: List files
        run: ls -la

      - name: Setup Pages
        uses: actions/configure-pages@v5

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: .

      - name: Deploy to GitHub Pages
        uses: actions/deploy-pages@v4
```

> 触发条件：推送到 `main` 自动部署。首次需在 Pages 面板确认启用。

### 方案 B：部署到 **项目子路径**（自动重写资源为相对路径）

在上传前替换 `index.html` 里出现的绝对资源路径为相对路径，避免 404。

```yaml
name: Deploy to GitHub Pages (Subpath Safe)

on:
  push:
    branches: [ main ]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      # 将绝对路径 "/assets" "/fonts" 等改为相对 "./assets" "./fonts"
      - name: Rewrite absolute asset paths for subpath hosting
        run: |
          # 仅需处理 index.html（样式与脚本都在其中）
          sed -i -E 's|href="/fonts|href="./fonts|g' index.html
          sed -i -E 's|href="/assets|href="./assets|g' index.html
          sed -i -E "s|url\\('/assets|url\\('\\./assets|g" index.html
          sed -i -E 's|src="/assets|src="./assets|g' index.html
          sed -i -E 's|src="/fonts|src="./fonts|g' index.html

      - name: Setup Pages
        uses: actions/configure-pages@v5

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: .

      - name: Deploy to GitHub Pages
        uses: actions/deploy-pages@v4
```

> 若你后续将资源放入独立的 `.css` / `.js` 文件里，也请一并替换其中的绝对路径（可追加 `sed`）。

---

## 常见问题

* **Q: GitHub Pages 打不开字体/背景图？**
  A: 你很可能部署在项目子路径，绝对路径 `/assets` 指向了根域。请用上文 **方案 B** 自动重写，或改用自定义根域名。

* **Q: 动效太多会不会影响可访问性？**
  A: 模板遵循 `prefers-reduced-motion`；系统选择“减少动态效果”时，脚本会跳过动画。

* **Q: 我能把背景图再压缩吗？**
  A: 可以。保证四档尺寸与命名一致即可。WebP 也可（同时更新引用与缓存头）。

* **Q: 想新增语言/卡片？**
  A: 语言在 `translations` 里添加同名键；卡片复制 `<article class="card">` 即可，注意相应的 `data-i18n` 文案键。

---

## 进阶

* **缓存优化**：见 `vercel.json`；Nginx/Caddy 也可为 `/assets` 与 `/fonts` 设置 `Cache-Control: public, max-age=31536000, immutable`。
* **SEO**：补充 OG/Twitter meta、`robots.txt`、`sitemap.xml`。
* **监控**：可加简单的纯前端统计（推荐自建或隐私友好方案）。

---

## 版权与致谢

* 代码：MIT（你可自由二次分发/修改）。
* 字体与图片：版权归各自作者所有，如替换为自己的资源更稳妥。

---

## 快速清单（TL;DR）

* 改 `index.html` 中的卡片链接、文案（配合 `translations`）。
* 替换 `assets/background/*` 与 `assets/favicon.svg`。
* **Vercel**：导入仓库 → Framework 选 “Other” → 无构建 → 部署。
* **GitHub Pages**：用上文 **Actions** 工作流，根域直接发布，**子路径请用“重写路径”方案 B**。
