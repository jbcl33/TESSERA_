# 百衲

站名 **百衲_**。嵌入式方向工作室的聚合站：首页 / 成员 / 关于 三页，成员个人站索引 + 团队介绍。

- 线上：https://jbcl33.github.io/TESSERA_/
- 仓库：https://github.com/jbcl33/TESSERA_ （默认分支 `main`，GitHub Pages 从 `main` 根目录发布）

风格参照 `C:\Users\Administrator\Projects\SIGNAL-NOISE`（GitHub `jbcl33/SIGNAL-NOISE`）首页：
深色终端、等宽字体、box-drawing 方框、用 C 源码表达导航，**无 JavaScript**。

## 本地预览

```bash
cd /f/tessera
python -m http.server 8765 --bind 127.0.0.1
# 打开 http://127.0.0.1:8765/
```

三页之间用相对链接互跳，直接双击 `index.html` 也能看（图标在 `file://` 下部分浏览器不加载，用上面的本地服务器最稳）。

## 页面

| 文件 | 导航（形状） | 内容 |
|---|---|---|
| `index.html` | ● 圆 | `index.c`：工作署名、`enum view`、两个入口（成员 / 关于） |
| `members.html` | ▲ 三角 | `members.c`：`Member` 结构体 + `members[]` 数组，成员站链接 |
| `about.html` | ■ 长方形 | `about.txt`：工作室在做什么、本站做什么、成员站点、加入方式 |

导航只留形状，**没有中文标签也没有下划线**：当前页是白实心，其他页是白色描边，
文字说明只留在 `aria-label` 里（屏幕阅读器可读，视觉上不显示）。
形状是内联 SVG（`circle` / `path` 三角形 / `rect` 长方形），`fill:currentColor` 与 `stroke:currentColor`，
悬停反白时形状跟着变黑。指针区域 28×28，移动端 44×44。

## 字体

全部用系统字体，不自托管、不下载任何 web font：

- 字体栈：`Consolas, SFMono-Regular, Liberation Mono, Noto Sans Mono CJK SC, monospace`
- 因此 Latin 是 Consolas、汉字回落到系统等宽中文字体（`fonts` 在页面上是 0 个）
- **品牌字例外**：`h1` 的中文用 `SimHei`（黑体，方形无衬线），并给 `h1` 单独开 `font-synthesis:weight`
  —— 因为全局 `font-synthesis:none` 会挡掉黑体（只有 Regular 一个字重）的合成粗，不开就没法“方正”
- 字号：正文/代码 13px（行高 1.85）、prompt 12px、tagline 13px、`.meta` 11px、`.colophon` 10px；
  移动端 420px 以下代码 11px、340px 以下 10px

## 文件

```
index.html                    首页
members.html                  成员
about.html                    关于
assets/site.css               三页共用样式（唯一一份 CSS，无内联）
assets/icon-original.png      原始图标，未做任何修改（来源：Downloads/1795325129451.png）
assets/icon.png               512×512 透明底，页头标志
assets/icon-opaque.png        512×512 不透明底（#0c0c0c），og:image 用
assets/apple-touch-icon.png   180×180 iOS 主屏图标（不透明底）
assets/favicon-64.png         64×64 PNG 图标（现代浏览器优先，透明底）
assets/favicon.ico            16/32/48/64 多尺寸图标（透明底）
```

图标派生流程（`icon-original.png` 是唯一来源）：
原图是 **透明底 + 纯白实心块**，所以直接拿 alpha 当形状遮罩 → 裁到内容包围盒（655×694）→
补边成正方形 → 缩放到各尺寸。网页与 favicon 保留透明背景（页面底色 `#0c0c0c` 透出来，不会出现色差方块）；
`og:image` 与 iOS 主屏图标用 `#0c0c0c` 不透明底，避免社交平台与 iOS 把透明区渲染成白色。

## 加人 / 改人

改 `members.html`，四处同步：

1. `const Member members[] = {...}` 加一条 `{ "站点名", "分工", "URL" }`
   - 尚未上线的写 `NULL`，并在 `main()` 用注释行替代链接（沿用 SIGNAL-NOISE 的 NULL 口径）
2. `const size_t members_count` 改实际人数
3. `const char updated[]` 改日期
4. `main()` 加一行 `open_member(&members[N]);` 链接（带 `target="_blank" rel="noopener"` 和 `aria-label`）

另外首页、成员页页脚的 `// 成员 N · 更新 YYYY-MM-DD`、以及 `about.html` 的成员站点段要同步。

当前成员：

| 站点名 | 分工 | URL |
|---|---|---|
| MCU / NOTES | 嵌入式 · STM32 · C | https://lxygu10101-cell.github.io/mcu-blog/ |
| EMBEDDED SHOWROOM | 嵌入式系统展示 | https://embedded-systems-showroom.swirlingflair.chatgpt.site/#lobby |

> `EMBEDDED SHOWROOM` 这个显示名是从域名推出来的，**待成员本人确认**。
> `MCU / NOTES` 的介绍文字取自其站点 `<meta name="description">`。

## 部署

已经接上 GitHub Pages：推送到 `main` 后自动发布，约 1 分钟生效。

- 站点：https://jbcl33.github.io/TESSERA_/
- 设置：Settings → Pages → Source = `Deploy from a branch`，Branch = `main`，目录 `/ (root)`
- 无构建步骤，推什么就上什么；改了 `index.html` 直接 push 即可
