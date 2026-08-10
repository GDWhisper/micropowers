# Install Micropowers

Copy each skill into the agent's skills directory so `/micropowers` and `/micropowers-brainstorm` are available.

## Pi

```bash
SKILLS_DIR="${HOME}/.agents/skills"
REPO_DIR="$(cd "$(dirname "$0")" && pwd)"

for skill in micropowers micropowers-brainstorm micropowers-plan micropowers-execute micropowers-finish; do
    cp -r "${REPO_DIR}/skills/${skill}" "${SKILLS_DIR}/${skill}"
done
```

> 注：`micropowers-styles/` 已作为子目录内嵌在 `micropowers/` 里，随 `micropowers` 一起复制，无需单独安装。

`npx skills` 方式（推荐，一键安装整套）：

```bash
npx skills add GDWhisper/micropowers -s '*'
```

> `-s '*'` 一次安装全部 5 个 skill（micropowers / micropowers-brainstorm / micropowers-plan / micropowers-execute / micropowers-finish），跳过 skill 勾选；**安装到哪个 agent 保留交互选择**（或 `-a <agent>` 预设）。装到全局加 `-g`。

If you fetched this file from a URL (not a local clone), clone the repo first:

```bash
git clone https://github.com/GDWhisper/micropowers /tmp/micropowers
cd /tmp/micropowers
```
then run the copy loop above.
