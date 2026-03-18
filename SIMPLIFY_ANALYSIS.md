# 网页工程分析与简化建议

## 一、工程概况

- **技术栈**：Jekyll 静态站（Hamilton 主题）
- **用途**：个人学术主页（Biography + 出版物展示）
- **实际使用**：首页 `index → home` 为主，无博客文章（`_posts` 已清空）

---

## 二、臃肿点分析

### 1. 皮肤系统（可大幅简化）

| 项目 | 说明 |
|------|------|
| **4 套皮肤** | `skin-daylight.scss` / sunrise / sunset / midnight，共 4 个 scss + 4 个编译后的 css |
| **按时间切换** | `_includes/head.html` 里约 20 行 JS，按小时加载不同 skin 的 css |
| **结果** | 用户多数时间只看到一种外观，却加载了整套切换逻辑和多个样式入口 |

**建议**：固定使用一套皮肤（如 daylight），删除按时间切换的 JS，以及 sunrise/sunset/midnight 的 scss/css，只保留一个 skin 入口。

---

### 2. 头部资源（head）偏重

| 项目 | 说明 |
|------|------|
| **Google Fonts** | 一次请求 9 种字体：Open Sans, Roboto, Roboto Slab, Inconsolata, Dancing Script, Noto Sans/Serif SC/TC, Ma Shan Zheng |
| **Font Awesome** | 整站引入 `all.js`（首页只用到少量图标） |
| **样式** | main.css + skin.css，以及皮肤切换时动态注入的 skin-xxx.css |

**建议**：字体只保留 1～2 种（如英文 + 中文各一）；若图标不多，可考虑用 SVG 或只引入 FA 子集。

---

### 3. 博客相关代码（当前未使用）

| 文件/目录 | 说明 |
|-----------|------|
| `_layouts/post.html` | 文章页（分类、标签、前后文、Disqus） |
| `_layouts/archive-taxonomies.html` | 分类/标签归档页 |
| `_layouts/archive-years.html` | 按年归档页 |
| `_includes/toc.html` | 约 170 行的目录生成逻辑，仅在有 `page.toc` 时用 |
| `_includes/sidebar-toc.html` | 依赖 toc.html 的侧边栏目录 |
| `_includes/disqus.html` | 评论（无文章则用不到） |
| `categories.md` / `tags.md` / `years.md` | 归档入口页，目前无文章可归档 |

**说明**：若确定不做博客、只做单页/多页展示，可删除或收缩上述布局与 include，只保留 `default`、`home`、`page` 和简单的 404。

---

### 4. 导航与配置

| 项目 | 说明 |
|------|------|
| `_data/navigation.yml` | 为空，但 header 仍保留完整导航结构（含下拉） |
| **config** | 含 jekyll-feed、jekyll-paginate 等，无文章时作用有限 |

**建议**：若暂无导航链接，可收拢 header 只保留站点标题/头像；不需要 RSS/分页时可从 `_config.yml` 去掉对应插件。

---

### 5. 其他

- **`_includes/custom-head.html`**：目前为空占位，可保留以便日后扩展。
- **macOS 资源文件**：`._index.html`、`.__config.yml`、`._home.html`、`._custom-styles.scss` 为系统生成，可加入 `.gitignore` 并删除，避免误提交。

---

## 三、简化优先级建议

| 优先级 | 操作 | 预期效果 |
|--------|------|----------|
| 高 | 固定单皮肤 + 去掉按时间切换 JS | 少 3 个 skin 样式、少 1 段脚本，首屏更干净 |
| 高 | 减少 Google Fonts 数量 | 减少外部请求与字体加载时间 |
| 中 | 收缩或移除博客布局/归档/TOC/Disqus | 减少模板与 include 数量，便于维护 |
| 中 | 导航为空时简化 header | 页面结构更贴合现状 |
| 低 | 清理 ._*、调整 config 插件 | 仓库更干净、构建更贴合实际 |

---

## 四、若只保留「首页 + 关于」的极简方案

可收敛为：

- **保留**：`default`、`home`、`page`（用于 about 等单页）、404、sidebar、footer、head（精简后）、`custom-styles`（出版物样式）。
- **删除或不再使用**：4 套皮肤改为 1 套、post/archive-* 布局、toc/sidebar-toc、disqus、mathjax（若不用公式）。
- **head**：单字体、单 skin.css、按需保留 FA 或改为 SVG。

这样工程会从「通用博客主题」收拢为「个人主页主题」，代码量和维护成本都会明显下降。

---

## 五、已实施的简化（本次修改）

- **固定单皮肤**：在 `_config.yml` 中设置 `skin: daylight`，并移除了 head 中按时间切换皮肤的脚本；删除 `_sass/hamilton/skins/` 下的 sunrise、sunset、midnight 以及 `assets/css/` 下对应的 4 个 skin-xxx.scss，只保留一套 daylight 样式。
- **精简 head**：Google Fonts 从 9 种改为 2 种（Open Sans + Noto Sans SC），减少请求与体积。
- **.gitignore**：已添加 `._*`，避免提交 macOS 资源 fork 文件。
