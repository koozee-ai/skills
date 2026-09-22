---
name: koozee-shared
description: "Use before any koozee-* skill: Node gate, login, JSON ok envelope. Do not hand-write canvas JSON."
metadata:
  requires:
    bins: ["koozee-cli"]
---

# koozee-cli 共享规则

所有 `koozee-*` Skill 的底座。未过本闸禁止装 CLI、禁止 `update`、禁止 Shortcut。

## Node 闸

Agent 应用能跑 ≠ 用户 shell PATH 里有 Node。以终端为准。

1. 跑 `node -v` 和 `npx -v`。需要 Node **≥18.18.0** 且 npx 可用。
2. 无 Node / 过低 / 无 npx：说明原因，展示将执行的命令，**用户显式同意后再跑**。拒绝 → 停。禁止 fnm/nvm/volta。禁止未确认 `curl | bash`。
3. 装完**新开 shell**再查一次 `node -v` / `npx -v`。过闸才装 CLI。
4. 用户侧只装 CLI，不管 Node 版本；代装只装官方 LTS 一次当运行时。

### darwin（官方 LTS pkg）

```bash
PKG=$(curl -fsSL https://nodejs.org/dist/latest-v22.x/SHASUMS256.txt | awk '/\.pkg$/{print $2; exit}')
curl -fL -o /tmp/nodejs-lts.pkg "https://nodejs.org/dist/latest-v22.x/${PKG}"
sudo installer -pkg /tmp/nodejs-lts.pkg -target /
```

### linux（官方 LTS 二进制）

```bash
ARCH=$(uname -m)
case "$ARCH" in x86_64) N=linux-x64 ;; aarch64|arm64) N=linux-arm64 ;; *) echo "unsupported arch: $ARCH"; exit 1 ;; esac
TAR=$(curl -fsSL https://nodejs.org/dist/latest-v22.x/SHASUMS256.txt | awk -v a="$N" '$2 ~ a && $2 ~ /tar.xz$/{print $2; exit}')
curl -fL -o /tmp/nodejs-lts.tar.xz "https://nodejs.org/dist/latest-v22.x/${TAR}"
sudo tar -xJf /tmp/nodejs-lts.tar.xz -C /usr/local --strip-components=1
```

## 首次安装

过 Node 闸后按这个顺序。不要自己 `npm i` 当升级。

1. `command -v koozee-cli` 没有 → `npx koozee-cli@latest`。
2. `npx skills add koozee-ai/skills -g --skill koozee-shared --skill koozee-canvas --skill koozee-workbench`
3. `koozee-cli auth login`（默认 `release`）。预发：`koozee-cli auth login --env pre`。

本仓开发：`pnpm --dir cli exec koozee-cli --version`。已装之后升用 `koozee-cli update`。

## 登录

未登录禁止 Shortcut。先 `koozee-cli auth status`；未登录则走上面的 `auth login`。环境只在登录时写入。凭证一份 `~/.config/koozee/credentials.json`。不要自己打开授权 URL。

## 契约

人在 Agent 对话里确认。CLI 不提问。判断成功用 **`ok == true`**。失败看 `error.hint`。

禁止手写画布 JSON、禁止传坐标、禁止自己拼 OpenAPI path、禁止自己订 SSE。怎么生成、怎么连线，读 `koozee-canvas`。

工作台独有工具先 `workbench +list` 再 `workbench +run --surface workbench`。人点头后才 `koozee-cli update`。不要自己 `npm i`。不要只升一边。扣豆前先 `+plan`，怎么生成读 `koozee-canvas`。

人要本机副本：用信封 `results[].url` 拉到用户指定的相对目录。不要绝对路径。不要静默覆盖。没有 `+download`。
