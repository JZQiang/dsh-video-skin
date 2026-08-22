# dsh 本地视频壁纸（dsh-video-skin）

一个 DSH 插件：把**本地上传的视频 / 图片**变成 **DSH 网页界面（`dsh web`）的背景壁纸**，配以 **iOS 风格液态玻璃**效果。

> 本版本已改造为**纯本地视频壁纸**：**不依赖 Steam / Wallpaper Engine**，打开设置页直接上传本地 MP4 / WebM / MOV / MKV / AVI 视频（或 JPG / PNG 图片）即可作为壁纸，开箱即用。

## 功能特性

- **本地视频壁纸**：上传本地视频（MP4 / WebM / MOV / MKV / AVI）或图片（JPG / PNG）作为 DSH 界面背景，无需 Steam、无需 Wallpaper Engine；
- **液态玻璃外观**：iOS 风格玻璃效果，配色（6 预设 + 自定义取色）与玻璃透明度（0–60%）即时生效；
- **壁纸选择弹窗**：缩略图网格收纳进独立弹窗；
- **隐藏 / 恢复**：不想看的壁纸一键隐藏（软删除），随时恢复，不碰源文件；
- **视频倍速**：0.5x – 2x 六档原生调速，即时生效、不重载；
- **水平翻转**：镜像画面（视频 / 图片均适用）；
- **画面适配**：覆盖 / 填充 / 居中 / 拉伸 四种模式；
- **自动轮转（轮播列表）**：自定义列表 + 切换间隔（1–120 分钟）+ 顺序/随机播放；
- **设置持久化**：全部设置保存在 `~/.dsh-wallpaper-engine/config.json`，重启 / 换端口 / 清浏览器数据都不丢失。

## 工作原理

- **Host 端**（`lib/index.js`）：一个 Cordis 插件，在 DSH webserver 上注册同源 HTTP 路由：
  - `GET /wallpaper-engine/inventory` → 壁纸 JSON 列表
  - `GET /wallpaper-engine/media/<token>` → 视频 / 图片（支持 Range 流式播放）
  - `GET /wallpaper-engine/preview/<token>` → 预览图
  - `POST /wallpaper-engine/upload` → 上传本地壁纸（视频 / 图片，原始字节流）
  - `POST /wallpaper-engine/remove` → 移除已上传的壁纸
  - `POST /wallpaper-engine/upload-dir` → 更改上传目录（持久化）
  - `GET/PUT /wallpaper-engine/settings` → 读写插件设置
- **Client 端**（`lib/client.js`）：浏览器模块，把选中壁纸渲染到应用三列**后方**的固定图层，并在「设置」里注册**一级设置页**「本地壁纸」（含上传、选择、外观、轮播等全部管理功能）。
- **自定义壁纸存储**：上传的文件写入插件管理的本地目录（默认 `~/.dsh-wallpaper-engine/uploads`，可在设置里改到任意盘符），经同一套 `/media`、`/preview` 路由服务——跨重启持久、无浏览器配额限制。

## 安装

### 普通用户（安装已发布版本）

```sh
dsh plugin --profile web add dsh-video-skin
```

装完重启 `dsh web`，打开 **设置 → 本地壁纸** 就能用。

### 开发者（运行本地代码）

```sh
dsh plugin --profile web add link:<插件文件夹绝对路径>
```

改完 `src/client.js` 后运行 `npm run build` 再重启 `dsh web`。

## 使用

1. 打开 `dsh web`，进入 DSH 界面。
2. 打开 **设置**，左侧导航里找到 **本地壁纸**。
3. 在「上传本地壁纸」区选择本地视频或图片，上传后自动成为背景壁纸。
4. 用 **暂停/播放** 暂停视频壁纸，用 **关闭** 清除壁纸。
5. 可调整：壁纸模糊、暗化、边框、玻璃、倍速、翻转、适配模式。

## 设置持久化

全部设置（已选壁纸、配色、透明度、布局、轮播、隐藏、倍速/翻转等）保存在 **`~/.dsh-wallpaper-engine/config.json`**（Windows：`C:\Users\<用户名>\.dsh-wallpaper-engine\config.json`），不依赖浏览器 localStorage——重启 / 换端口 / 清浏览器数据都不丢失。

## 配置

本插件不会向模型暴露任何工具或提示文本，对 agent 零 token 开销。唯一的本地落盘数据是**自定义壁纸文件**（存于你设置的上传目录）与记录该目录位置的 `~/.dsh-wallpaper-engine/config.json`。

## 开发 / 重建

- host 端（`lib/index.js`）是纯 ESM，无需构建；
- client 端（`lib/client.js`）是编译产物，由 `src/client.js` 经 `scripts/build-client.mjs` 生成：

```sh
npm run build      # 从 src/client.js 重新生成 lib/client.js
npm run verify     # 物化生成的 bundle 并断言其导出
```

编辑 `src/client.js` 后运行 `npm run build`，不要手改 `lib/client.js`。

## 许可

MIT
