# 视觉伴侣使用指南（Visual Companion）

micropowers-brainstorm 的可选浏览器工具。在用户同意的前提下启用，用于展示 mockup、图表和视觉选项对比。**它不是默认模式，是一个工具**——只在"看比读更易懂"时才拿出来用。

---

## 1. 概述

视觉伴侣是一个本地小服务器，把写到磁盘的 HTML 片段实时推到用户浏览器里，让用户看 mockup / 架构图 / 并排对比，并可以点击选择选项。

- **可选且需同意**：默认不启动。只有用户明确同意用浏览器辅助 brainstorm 时才启动（`start-server.sh --open`）。
- **只是一个工具**：绝大多数 brainstorm 用终端就够了。视觉伴侣只服务于"视觉"问题。
- **轻量**：没有 superpowers 那套 heavy ceremony。需要时就开，不需要就关。

---

## 2. 何时使用（per-question 决策）

**逐问题决策，不是逐会话。** 核心测试一句话：

> **用户看了是否比读文字更易懂？**

**用浏览器**（内容本身是视觉的）：

- **UI mockup** —— 线框图、布局、导航结构、组件设计
- **架构图** —— 系统组件、数据流、关系图
- **并排视觉对比** —— 两个布局、两套配色、两个设计方向直接摆在一起比
- **设计打磨** —— 问题是观感、间距、视觉层级时
- **空间关系** —— 状态机、流程图、ER 图这类用图比用文字清楚的东西

**用终端**（内容是文字或表格的）：

- **需求 / 范围问题** —— "X 是什么意思？"、"哪些功能在范围内？"
- **概念 A/B/C 选择** —— 在文字描述的不同方案间挑
- **权衡列表** —— 利弊、对比表
- **技术决策** —— API 设计、数据建模、架构选型
- **澄清问题** —— 答案本来就是文字、不是视觉偏好的

**关键区分**：UI 话题 ≠ 视觉问题。

- "你想要什么样的向导？" → 概念问题，用**终端**。
- "这几个向导布局哪个更顺眼？" → 视觉问题，用**浏览器**。

---

## 3. 如何启动（just-in-time）

**只在用户同意后才启动。** 启动命令：

```bash
bash skills/micropowers-brainstorm/scripts/start-server.sh --project-dir /path/to/project --open
```

- `--open`：首屏就绪时自动开浏览器。**仅当用户同意时才用**——脚本内部靠 `BRAINSTORM_OPEN` 开关，没它不会自动开。
- `--project-dir`：把 mockup 落到 `<project>/.micropowers/brainstorm/` 下，持久化、且重启复用同端口（已打开的标签页会自动重连）。不加则落到 `/tmp`，停服即清。
- 远程 / 容器环境：`--host 0.0.0.0 --url-host localhost` 绑定非 loopback 并控制打印出来的主机名。
- 空闲超时：默认 4 小时（`--idle-timeout-minutes` 可调），无活动自动退出。

**返回 JSON**（stdout 末行，也写入 `$STATE_DIR/server-info`）：

```json
{"type":"server-started","port":52341,"host":"127.0.0.1",
 "url_host":"localhost",
 "url":"http://localhost:52341/?key=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
 "screen_dir":"/path/to/project/.micropowers/brainstorm/12345-1700000000/content",
 "state_dir":"/path/to/project/.micropowers/brainstorm/12345-1700000000/state",
 "idle_timeout_ms":14400000}
```

真实字段（基于 `server.cjs:681`）：`type`、`port`、`host`、`url_host`、`url`、`screen_dir`、`state_dir`、`idle_timeout_ms`。

- **存好 `screen_dir` 和 `state_dir`**：前者是你写 HTML 的地方，后者是读用户点击事件的地方。
- **`url` 含 `?key=`**：见第 7 节安全提醒。
- 后台启动若没捕获 stdout，读 `$STATE_DIR/server-info` 拿 URL/port。

**提醒用户加 .gitignore**：`.micropowers/` 落了 mockup，建议加入 `.gitignore`（若尚未忽略）。

**停止服务器**：

```bash
bash skills/micropowers-brainstorm/scripts/stop-server.sh <session_dir>
```

注意这里传的是 **session 目录**（即 `screen_dir` / `state_dir` 的父目录，`.micropowers/brainstorm/<session-id>`），不是 `state_dir`。脚本会校验 PID 确为本会话服务器后再终止；只有 `/tmp` 下的会话目录会物理删除，`.micropowers/` 下的保留供回看。

---

## 4. The Loop（操作循环）

1. **确认服务器活着**，然后**写新 HTML 到 `screen_dir`**：
   - 先确认 `$STATE_DIR/server-info` 存在、且 `$STATE_DIR/server-stopped` 不存在。若已关停，用**同一个 `--project-dir`** 重启即可复用同端口，用户已开的标签页会自己重连（停机时显示 "Companion paused" 遮罩）。
   - 用**语义化文件名**：`platform.html`、`visual-style.html`、`layout.html`。
   - **绝不复用文件名**——每屏一个新文件。迭代就加版本后缀 `layout-v2.html`。
   - 用文件创建工具写，**不要 cat/heredoc**（会把噪音灌进终端）。
   - 服务器按修改时间自动服务最新文件。

2. **回合结束前告诉用户**：
   - 复述 URL（每步都给，不止第一次）。
   - 一句屏上内容简述，例如"展示首页的 3 个布局选项"。
   - 请用户在终端反馈："看一下，有想法告诉我，想选哪个选项直接点。"

3. **下一回合**：读取并合并两类反馈——
   - `state_dir/events`：用户浏览器点击记录（JSON 行）。新屏写入时该文件会被服务器清空。
   - 终端文本：主要反馈来源。
   - 终端优先，`events` 提供结构化交互数据。

4. **迭代或推进**：反馈改变了当前屏就写新文件（如 `layout-v2.html`）。当前步骤验证过了才进下一题。

5. **回到终端时清屏**：下一步不需要浏览器（澄清问题、权衡讨论）时，推一个 waiting 屏清掉陈旧内容：

   ```html
   <div style="display:flex;align-items:center;justify-content:center;min-height:60vh">
     <p class="subtitle">Continuing in terminal...</p>
   </div>
   ```

   避免用户对着已解决的选择发呆。下一个视觉问题来了再正常推新文件。

6. 重复直到结束。

---

## 5. 写内容片段

**默认写片段**（不含 `<html>` 的纯内容）。服务器检测到文件不以 `<!DOCTYPE` / `<html` 开头时，自动套用 `frame-template.html`（含 header、主题 CSS、连接状态、`helper.js` 全部交互设施）。只有需要完全掌控整页时才写完整文档。

**最小 A/B 选项示例**：

```html
<h2>Which layout works better?</h2>
<p class="subtitle">Consider readability and visual hierarchy</p>

<div class="options">
  <div class="option" data-choice="a" onclick="toggleSelect(this)">
    <div class="letter">A</div>
    <div class="content">
      <h3>Single Column</h3>
      <p>Clean, focused reading experience</p>
    </div>
  </div>
  <div class="option" data-choice="b" onclick="toggleSelect(this)">
    <div class="letter">B</div>
    <div class="content">
      <h3>Two Column</h3>
      <p>Sidebar navigation with main content</p>
    </div>
  </div>
</div>
```

- 点击捕获靠 `data-choice` 属性（`helper.js:130` 监听 `[data-choice]`）。
- `toggleSelect(this)`（`helper.js:146`）处理选中高亮；单选时清掉同容器内其他选中，多选需容器加 `data-multiselect`。
- 用户点击后，浏览器通过 WebSocket 上报 `{type:"click", choice, text, timestamp}` 到 `state_dir/events`。

---

## 6. 可用 CSS 类

以下类来自 `frame-template.html`（已实际核对，未编造）：

**选项（A/B/C 选择）**
- `.options` —— 纵向排列的选项容器；加 `data-multiselect` 可多选
- `.option` —— 单个选项卡片（hover 变蓝、`.selected` 高亮）
- `.option .letter` —— 左侧 A/B/C 字母块
- `.option .content` —— 右侧文字区（含 `h3` / `p`）

**卡片（展示设计 / mockup）**
- `.cards` —— 自适应网格（最小 280px 列）
- `.card` —— 单个设计卡（`.selected` 高亮）
- `.card-image` —— 卡头图区（16:10）
- `.card-body` —— 卡文字区（含 `h3` / `p`）

**Mockup 容器**
- `.mockup` —— 圆角边框容器（`.mockup-header` 标题条 + `.mockup-body` 内容区）

**并排对比**
- `.split` —— 两列网格（窄屏自动堆叠为单列）

**利弊**
- `.pros-cons` —— 两列利弊格
- `.pros` / `.cons` —— 各含 `h4` 与 `ul`

**占位 / 线框元件**
- `.placeholder` —— 虚线占位块
- `.mock-nav` —— 导航条元件
- `.mock-sidebar` —— 侧栏元件
- `.mock-content` —— 主内容区元件
- `.mock-button` —— 按钮元件
- `.mock-input` —— 输入框元件

**排版与区块**
- `h2` —— 页标题（1.5rem）
- `h3` —— 区块标题（1.1rem）
- `.subtitle` —— 标题下方次级灰字
- `.section` —— 带底边的区块
- `.label` —— 小号大写标签

**框架固定（非内容类，仅供了解）**
- `.brand` `.status` `.header` `.main` `#frame-content` —— 由 frame 模板注入，内容片段无需关心。

---

## 7. 安全提醒

- **URL 含 session key（`?key=…`）**：服务器对所有 HTTP 与 WebSocket 请求都校验该 key（`server.cjs:341` `isAuthorized`，key 同时存在于 query 与 cookie，皆常量时间比较）。
- **必须给完整 URL**：永远把 `url` 字段里的整条（含 `?key=`）交给用户。**绝不**剥掉 query，也绝不发裸的 `http://host:port`。裸地址会被服务器以 403 `FORBIDDEN_PAGE` 拒绝。
- **key 同时 gating HTTP 与 WebSocket**：无 key 的请求既读不到屏也发不进事件；这也挡住了同一网络里误开标签页或别的机器注入事件。
- **首次加载后**浏览器通过 cookie（`HttpOnly; SameSite=Strict`）记住 key，刷新与 `/files/*` 子资源免重复带 query，但你要给用户看的链接仍然是带 key 的完整 URL。
- 会话文件（`server-info`、`.last-token` 等）含 key，脚本以 `umask 077` 创建为仅 owner 可读。

---

## 参考

- 框架模板（CSS 参考）：`scripts/frame-template.html`
- 客户端脚本（点击 / WebSocket）：`scripts/helper.js`
- 服务器实现：`scripts/server.cjs`
- 启停脚本：`scripts/start-server.sh` / `scripts/stop-server.sh`
