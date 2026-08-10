# 视觉伴侣（Visual Companion）服务器

这是 micropowers `micropowers-brainstorm` skill 的**可选**视觉伴侣组件。它提供一个本地
HTTP + WebSocket 服务器，把 AI 在 brainstorm 阶段生成的 HTML mockup 实时推送到用户的浏览器，
并支持用户通过点击选项把选择事件回传给 AI。

**默认不启用**，只在用户明确同意开启视觉伴侣时才启动。

## 这是什么

- 一个零依赖的 Node.js 服务器（`server.cjs`），只用 Node 内置模块
  （`crypto` / `http` / `fs` / `path` / `os` / `child_process`），无需 `npm install`。
- 浏览器端 `helper.js` 通过 WebSocket 与服务器保持连接：文件更新时自动刷新、断线自动重连、
  捕获 `data-choice` 点击并回传为 `events`。
- `frame-template.html` 是统一的页面框架模板（顶栏品牌 + 连接状态 + 内容区）。

## 如何被 SKILL 调用

在 `micropowers-brainstorm` 的 SKILL.md 流程里，仅在用户同意使用视觉伴侣时，由 skill 执行
`start-server.sh`：

```bash
# 临时会话（文件落在 /tmp，停止后清理）：
bash skills/micropowers-brainstorm/scripts/start-server.sh

# 持久会话（mockup 留在项目目录，便于事后回看）：
bash skills/micropowers-brainstorm/scripts/start-server.sh --project-dir <项目路径>
```

启动成功后脚本输出一行 JSON（同时写入 `state/server-info`）：

```json
{
  "type": "server-started",
  "port": 51824,
  "host": "127.0.0.1",
  "url_host": "localhost",
  "url": "http://localhost:51824/?key=.....",
  "screen_dir": ".../.micropowers/brainstorm/<session>/content",
  "state_dir": ".../.micropowers/brainstorm/<session>/state",
  "idle_timeout_ms": 14400000
}
```

skill 应：
1. 把完整 `url`（含 `?key=`）交给你给用户打开。**key 是会话密钥，缺它服务器返回 403。**
2. 把要展示的 HTML mockup 写入 `screen_dir`（`*.html`），服务器会用最新文件自动推送给浏览器。
3. 读取 `state_dir/events` 回看用户的选择事件。
4. 会话结束后调用 `stop-server.sh <session_dir>` 停止（或直接依赖 4 小时空闲自动退出）。

### 常用参数

- `--project-dir <path>`：会话文件落在 `<path>/.micropowers/brainstorm/` 下，停止后保留。
- `--host <bind-host>`：绑定地址（默认 `127.0.0.1`；远程/容器用 `0.0.0.0`）。
- `--url-host <host>`：返回 URL 里显示的主机名。
- `--idle-timeout-minutes <n>`：空闲多少分钟后自动退出（默认 240 = 4 小时）。
- `--open`：**仅在用户同意后**使用，首个屏幕就绪时自动打开浏览器。
- `--foreground` / `--background`：前台或后台运行。

## 依赖

- 仅 Node.js（建议 v16+，用到 `BigInt` 与 `fs.realpathSync` 等内置能力）。**无任何 npm 依赖。**

## 用户如何停止

1. 运行 `bash skills/micropowers-brainstorm/scripts/stop-server.sh <session_dir>`
   （`session_dir` 即启动 JSON 里的 `state_dir` 去掉 `/state` 后缀）。
2. 或不操作，服务器在 **4 小时无活动后自动退出**（可用 `--idle-timeout-minutes` 调整）。

## 安全模型

- 每个会话生成随机 `key`，以 `?key=` 形式内嵌在 URL 中，并通过 cookie 镜像。
- 任何 HTTP 请求与 WebSocket 升级都需携带正确 key（常量时间比较），否则返回 403。
- `--project-dir` 模式下的 port / token 文件权限为 `0600`，仅属主可读。
- 服务器持续检查 owner 进程是否存活，owner 退出即关闭；也能抵御 DNS 重绑定（依赖 key 而非 Host 白名单）。

## 文件清单

| 文件 | 作用 |
|------|------|
| `server.cjs` | 服务器主程序（HTTP + 手搓 WebSocket 协议） |
| `start-server.sh` | 启动脚本，解析参数、生成会话目录、输出连接 JSON |
| `stop-server.sh` | 停止脚本，按会话实例 id 精确匹配进程后优雅退出 |
| `helper.js` | 浏览器端 JS：WebSocket 重连、事件捕获与回传 |
| `frame-template.html` | 页面框架模板（顶栏品牌 / 状态 / 内容区） |
