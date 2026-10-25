# Install Micropowers

Copy the single `micropowers` skill into the agent's skills directory so `/micropowers` is available.

## Manual copy (Pi, Claude Code, Cursor, ...)

```bash
SKILLS_DIR="${HOME}/.agents/skills"
REPO_DIR="$(cd "$(dirname "$0")" && pwd)"

mkdir -p "${SKILLS_DIR}"
cp -r "${REPO_DIR}/skills/micropowers" "${SKILLS_DIR}/micropowers"
```

> 注：整个工作流（四个阶段 + 四种风格 + 视觉伴侣）都是 `micropowers/` 目录内的子目录，随它一起复制，无需单独安装。
>
> **从旧版本升级时**，请先删掉旧的平级副本，否则它们会以独立 skill 的身份继续被加载：
>
> ```bash
> rm -rf ~/.agents/skills/micropowers-brainstorm ~/.agents/skills/micropowers-plan \
>        ~/.agents/skills/micropowers-execute ~/.agents/skills/micropowers-finish
> ```

`npx skills` 方式（推荐，一键安装）：

```bash
npx skills add GDWhisper/micropowers -s '*'
```

> `-s '*'` 跳过 skill 勾选，直接安装全部（现在只有 `micropowers` 一个）；**安装到哪个 agent 保留交互选择**（或 `-a <agent>` 预设）。装到全局加 `-g`。

## Pi package

Pi 用户可以不复制文件，直接装成 pi 包（skills 由 pi 自己加载、可 `pi update` 升级）：

```bash
pi install npm:micropowers
```

> 也可以钉住 git tag：`pi install git:github.com/GDWhisper/micropowers@v0.1.0`。加 `-l` 写入当前项目的 `.pi/settings.json`，默认写入用户级 settings。
> **不要和上面的复制方式同时用。** pi 里同名 skill 先到先得，且自动发现目录优先于包 —— `~/.agents/skills/micropowers*` 或 `~/.pi/agent/skills/micropowers*` 下的残留副本会静默屏蔽包里的 skill。切换到 pi 包之前先删掉它们：
>
> ```bash
> rm -rf ~/.agents/skills/micropowers* ~/.pi/agent/skills/micropowers*
> ```

If you fetched this file from a URL (not a local clone), clone the repo first:

```bash
git clone https://github.com/GDWhisper/micropowers /tmp/micropowers
cd /tmp/micropowers
```
then run the copy command above.
