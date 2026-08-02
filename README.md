# About Sen · 3D个人简历

**简体中文** · [English](README.en.md)

一个基于 **React Three Fiber** 的滚动式个人3D简历。

<h3 align="center">
  🔗 <a href="https://dayinji.github.io/sen-3d-resume/">点击进入在线预览 →</a>
</h3>

> 💡 **不想写代码、不想折腾环境？** 可以试试 [intro3d.com](https://intro3d.com)：一个零代码的 3D 个人主页 DIY 平台，小白也能上手，一站式解决制作与部署。相比本项目少了「眼球跟随光标」的互动，其余相对完整。想快速拥有一个类似的 3D 简历，它更省时；想完全掌控、深度定制，再用本仓库源码。

## 开源说明（先读这段）

- **代码**采用 **MIT** 许可证，见 [`LICENSE`](LICENSE)，欢迎学习、复用、二次开发。
- **个人内容与素材**（姓名 / 人物模型 / 简历 / 作品文案 / 品牌 logo / 社交链接）**不在 MIT 范围内**，版权归作者所有。fork 后请替换成你自己的内容，详见 [`NOTICE`](NOTICE)。

## 仓库结构

```
web/       前端应用（React Three Fiber + TypeScript，见下文）
blender/   场景三维源文件 sen.blend（人物 + 相机动画 + 对焦锚点）
tutor/     改造教程（部署、贴纸等）
CLAUDE.md AGENTS.md          面向 AI 编码助手的协作约定
README.md README.en.md       说明（简体中文 / English）
LICENSE NOTICE               许可与内容声明
```

前端应用整个在 [`web/`](web) 下。**下文提到的代码 / 资源路径都相对 `web/`**（如 `src/App.tsx` 即 `web/src/App.tsx`），npm 命令也在 `web/` 里执行。

## 运行

```bash
cd web
npm install
npm run dev        # 开发 http://localhost:5173
npm run build      # 类型检查 + 打包，产物输出到 dist/
npm run preview    # 预览 build 产物
npm run typecheck  # 仅类型检查（tsc -b）
npm run lint       # ESLint
```

## 架构总览

纯前端 SPA，无后端、无路由：`index.html` → `src/main.tsx` → `src/App.tsx`（一个固定 `<Canvas>` 3D 背景 + 可滚动 HTML 叠层）。

- **3D 背景**：`App.tsx` 里当前渲染的是 `src/scene/Scene.tsx`（`me.glb` 人物 + 滚动驱动的相机 / 眼睛跟随）。
- **滚动内容**：`Hero`（About，`App.tsx` 内）→ `src/ui/Resume.tsx`（履历时间轴）→ `src/ui/Works.tsx`（作品集横向画廊）。
- **叠层效果**：`LoadingScreen`（加载遮罩）、`NoiseOverlay`（胶片噪点）、滚动渐暗 / 磨砂右轨 / 首屏装饰画框等。
- **全局状态**：`src/store.ts`（zustand，轻量）。

## 数据驱动：改内容改哪里

整个站点内容与表现分离，改内容基本只动数据文件：

| 想改什么 | 改哪里 |
| --- | --- |
| 履历（学历 / 经历 / 客户 / 社交链接） | `src/ui/Resume.tsx` |
| 作品集板块与作品列表 | `src/data/works.ts` |
| 单个作品的详情正文 | `src/content/works/<slug>.md`（frontmatter + markdown；格式见 `src/data/workDocs.ts`，示例见 `src/content/works/example.md`） |
| 首屏 About 文案 | `src/App.tsx` 里的 `COPY` |

作品详情用极简 markdown：每个作品一个 `.md`，通过 `works.ts` 里 item 的 `slug` 关联；没有 `slug` 的作品走占位详情。媒体（图 / 视频）放 `public/works/` 下，用 `/works/...` 绝对路径引用。

## 目录结构

```
web/                     前端应用（以下路径都在 web/ 下）
  src/
    App.tsx              Canvas + 滚动内容装配、首屏 About、加载/叠层
    main.tsx             入口
    store.ts             全局交互状态（zustand）
    data/
      works.ts           作品集板块 / 作品列表（作品集数据源）
      workDocs.ts        构建期内联 content/works/*.md + frontmatter 解析
    content/works/       作品详情 markdown；含 example.md 模板。放入 <slug>.md（slug 对应 works.ts）即渲染完整详情，否则显示统一占位
    scene/
      Scene.tsx          3D 场景：me.glb 人物 + glb 相机动画 + 滚动驱动 / 眼睛跟随
      Env.tsx            env.hdr 环境光照（IBL）
    ui/
      Resume.tsx         履历时间轴（含个人数据）
      Works.tsx          作品集横向画廊 + 详情弹窗
      LoadingScreen.tsx / NoiseOverlay.tsx / SocialIcons.tsx / ZooopLogo.tsx
  public/
    models/  fonts/  images/  textures/   静态素材
  scripts/compress-media.sh                媒体压缩脚本（ffmpeg，原地压缩）
blender/sen.blend        场景三维源文件（改完导出 web/public/models/me.glb）
tutor/                   改造教程（部署、贴纸等）
```

## 素材与媒体（重要）

- **作品详情默认是占位。** 开源版本不含作者的作品详情与媒体：作品集画廊保留各板块 / 作品**标题**，点开详情页统一显示占位文案（改 `works.ts` 的 `detailPlaceholder`）。想填充某个作品，在 `src/content/works/<slug>.md`（slug 对应 `works.ts` 里 item 的 `slug`）写 frontmatter + markdown，媒体放 `public/works/` 下用 `/works/...` 引用，即自动渲染成完整详情。
- **`public/works/` 默认不入 git**（体积大且属个人内容，见 `.gitignore`）——仅保留 4 张板块封面图 `public/works/covers/*.jpg`；其余媒体请自行放置或走 CDN。
- `public/models/`、`public/images/`、`public/textures/`、`public/fonts/` 里的素材已入库。其中人物模型、品牌 logo、图片属于个人内容（见 `NOTICE`）；字体 / HDR 为第三方素材，复用前请各自核对许可。
- `scripts/compress-media.sh` 用 ffmpeg 原地压缩 `public` 下的图 / 视频（最大宽 1920，视频 H.264 ~2Mbps），仅当压得更小才覆盖。

## 换人物模型（glb 约定）

想换成你自己的人物，替换 `public/models/me.glb`（源文件是仓库根的 `blender/sen.blend`，在 Blender 里改完导出 glb 覆盖它），或改写 `src/scene/Scene.tsx` 用你自己的场景。代码按**对象名字**在 glb 里查找以下内容，缺哪个对应功能就失效：

- **相机 + 相机动画 clip（名为 `CameraAction`）** —— 滚动驱动的镜头路径；总帧数运行时按 24fps 从 clip 读取，不写死。
- **`focus-start`**（或 `focus-0`）—— 首页对焦锚点（空对象），两种命名都认。
- **时间轴对焦锚点**（空对象，每条履历一个）—— 顺序列在 **`src/data/focusPoints.ts`** 的 `FOCUS_POINTS`（`Scene.tsx` 与 `Resume.tsx` 共用的唯一真源）。**条数是动态的**：同时增删这里与 `Resume.tsx` 的 entries，节点数与帧区间会自动适配。本仓库自带 `focus-1 / focus-2 / focus-3 / focus-4 / focus-5`（首页锚点另用 `focus-0`）。
- **`focus-works`** —— 作品区对焦锚点（空对象）；可选，缺省时作品区自动复用末时间轴锚点。
- **名字含 `eye` 的网格** —— 眼睛（眼睛跟随鼠标）。
- **相机帧约定**（做 `CameraAction` 动画时遵循）：第 `0` 帧=首页(`focus-start`)、第 `50·k` 帧=第 k 个时间轴节点、**最后一帧**=作品区(`focus-works`)。即节点间隔 50 帧，最后节点到末帧是作品区帧段（可长可短）。

## 构建与部署

```bash
npm run build    # → dist/
```

`vite.config.ts` 里 `base: './'`，产物用相对路径，`dist/` 可直接双击打开、也可放到任意子目录（如部署到 `example.com/portfolio/`）。运行时 public 资源用 `import.meta.env.BASE_URL` 拼接。部署就是把 `dist/` 传到任意静态托管（Netlify / Vercel / GitHub Pages / 对象存储 / 自有服务器）即可。

## 场景参数

场景所有可调参数（灯光 / 相机 / 景深 / Bloom / 人物位置 / 背景渐变色等）都是 `src/scene/Scene.tsx` 里各组件顶部的**普通常量**。想改画面默认表现，直接改这些常量的值即可，无需任何面板或额外配置文件。

## 技术栈

React 18 · TypeScript · @react-three/fiber · @react-three/drei · @react-three/postprocessing · three · framer-motion · zustand · Vite
