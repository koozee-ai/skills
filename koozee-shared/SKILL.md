---
name: koozee-shared
description: "Use before koozee-canvas or koozee-workbench when generating with Koozee. Node gate, login, JSON ok envelope. Do not hand-write canvas JSON."
---

# koozee-cli 共享规则

所有 `koozee-*` Skill 的底座。未过本闸禁止装 CLI、禁止 `update`、禁止 Shortcut。

## Node 闸

Agent 应用能跑 ≠ 用户 shell PATH 里有 Node。以终端为准。

1. 跑 `node -v` 和 `npx -v`。需要 Node **≥18.18.0** 且 npx 可用。够了就不装。
2. 无 Node / 过低 / 无 npx：直接跑下面对应系统的脚本。先系统安装。安装包没下到本地就停，不要兜底。已经在本地且安装器退出非 0（取消或没有管理员权限）才解到 `$HOME/.koozee/node`。不要 `sudo`，不要改 PATH。不要等人同意。禁止 fnm/nvm/volta。禁止 `curl | bash`。
3. 系统安装成功：新开 shell 再查 PATH 上的 `node -v` / `npx -v`。走了兜底：查 `"$HOME/.koozee/node/bin/node" -v` 和同目录的 `npx`。Windows 查 `%USERPROFILE%\.koozee\node\node.exe` 和 `npx.cmd`。不要再信 PATH。过闸才装 CLI。
4. 用户侧只装 CLI，不管 Node 版本；代装只装官方 LTS 一次当运行时。

### darwin

```bash
PKG=$(curl -fsSL https://nodejs.org/dist/latest-v22.x/SHASUMS256.txt | awk '/\.pkg$/{print $2; exit}')
curl -fL -o /tmp/nodejs-lts.pkg "https://nodejs.org/dist/latest-v22.x/${PKG}" || exit 1
if ! sudo installer -pkg /tmp/nodejs-lts.pkg -target /; then
  ARCH=$(uname -m)
  case "$ARCH" in arm64) N=darwin-arm64 ;; x86_64) N=darwin-x64 ;; *) echo "unsupported arch: $ARCH"; exit 1 ;; esac
  TAR=$(curl -fsSL https://nodejs.org/dist/latest-v22.x/SHASUMS256.txt | awk -v a="$N" '$2 ~ a && $2 ~ /tar.xz$/{print $2; exit}')
  curl -fL -o /tmp/nodejs-lts.tar.xz "https://nodejs.org/dist/latest-v22.x/${TAR}" || exit 1
  mkdir -p "$HOME/.koozee/node"
  tar -xJf /tmp/nodejs-lts.tar.xz -C "$HOME/.koozee/node" --strip-components=1
  "$HOME/.koozee/node/bin/node" -v
  "$HOME/.koozee/node/bin/npx" -v
fi
```

### linux

```bash
ARCH=$(uname -m)
case "$ARCH" in x86_64) N=linux-x64 ;; aarch64|arm64) N=linux-arm64 ;; *) echo "unsupported arch: $ARCH"; exit 1 ;; esac
TAR=$(curl -fsSL https://nodejs.org/dist/latest-v22.x/SHASUMS256.txt | awk -v a="$N" '$2 ~ a && $2 ~ /tar.xz$/{print $2; exit}')
curl -fL -o /tmp/nodejs-lts.tar.xz "https://nodejs.org/dist/latest-v22.x/${TAR}" || exit 1
if ! sudo tar -xJf /tmp/nodejs-lts.tar.xz -C /usr/local --strip-components=1; then
  mkdir -p "$HOME/.koozee/node"
  tar -xJf /tmp/nodejs-lts.tar.xz -C "$HOME/.koozee/node" --strip-components=1
  "$HOME/.koozee/node/bin/node" -v
  "$HOME/.koozee/node/bin/npx" -v
fi
```

### windows

```powershell
$ErrorActionPreference = 'Stop'
$arch = switch ($env:PROCESSOR_ARCHITECTURE) {
  'AMD64' { 'x64' }
  'ARM64' { 'arm64' }
  default { throw "unsupported arch: $env:PROCESSOR_ARCHITECTURE" }
}
$sums = (Invoke-WebRequest -UseBasicParsing https://nodejs.org/dist/latest-v22.x/SHASUMS256.txt).Content
$msi = ([regex]::Match($sums, "(?m)^[0-9a-f]+ +(\S+-$arch\.msi)\s*$")).Groups[1].Value
if (-not $msi) { throw 'msi name not found' }
Invoke-WebRequest -UseBasicParsing -OutFile "$env:TEMP\nodejs-lts.msi" "https://nodejs.org/dist/latest-v22.x/$msi"
$installed = $false
try {
  $p = Start-Process msiexec.exe -ArgumentList "/i `"$env:TEMP\nodejs-lts.msi`" /qn" -Wait -PassThru -Verb RunAs
  if ($p.ExitCode -eq 0 -or $p.ExitCode -eq 3010) { $installed = $true }
} catch {
  $installed = $false
}
if (-not $installed) {
  $win = if ($arch -eq 'x64') { 'win-x64' } else { 'win-arm64' }
  $zip = ([regex]::Match($sums, "(?m)^[0-9a-f]+ +(\S+-$win\.zip)\s*$")).Groups[1].Value
  if (-not $zip) { throw 'zip name not found' }
  Invoke-WebRequest -UseBasicParsing -OutFile "$env:TEMP\nodejs-lts.zip" "https://nodejs.org/dist/latest-v22.x/$zip"
  $dir = Join-Path $env:USERPROFILE '.koozee\node'
  New-Item -ItemType Directory -Force -Path $dir | Out-Null
  $unpack = Join-Path $env:TEMP 'nodejs-lts-unpack'
  if (Test-Path $unpack) { Remove-Item -Recurse -Force $unpack }
  Expand-Archive -Path "$env:TEMP\nodejs-lts.zip" -DestinationPath $unpack -Force
  $inner = Get-ChildItem $unpack -Directory | Select-Object -First 1
  Copy-Item -Path (Join-Path $inner.FullName '*') -Destination $dir -Recurse -Force
  & (Join-Path $dir 'node.exe') -v
  & (Join-Path $dir 'npx.cmd') -v
}
```

## 首次安装

过 Node 闸后按这个顺序。不要 `npm i -g`。不要改 PATH。不要为装 Skill 去装 git。走了兜底时，下面的 `npm` / `npx` 换成 `"$HOME/.koozee/node/bin/npm"` 和 `"$HOME/.koozee/node/bin/npx"`。Windows 换成 `%USERPROFILE%\.koozee\node\npm.cmd` 和 `npx.cmd`。

可执行文件固定为当前项目的 `.koozee/node_modules/.bin/koozee-cli`。本文件和另外两份 Skill 里写的 `koozee-cli` 都换成这个路径再执行。安装前缀是这个 `.koozee`，不要装进 `$HOME/.koozee`。

1. 在项目根，这个文件不存在 → `npm install --prefix .koozee koozee-cli@latest`。
2. 用刚装好的可执行文件跑 `update`。它把 `koozee-shared`、`koozee-canvas`、`koozee-workbench` 拷进当前项目的 `.agents/skills`。只有 Claude 改写 `.claude/skills`。不要 `npx skills add -g`。
3. `.koozee/node_modules/.bin/koozee-cli auth login`（默认 `release`）。预发加 `--env pre`。本机开发加 `--env dev`，打开 `https://dev.koozee.ai`，接口打 `http://localhost:4000`。

本仓开发：`pnpm --dir cli exec koozee-cli --version`。当前项目里的 CLI 会自己升。不要自己 `npm i`。要马上对齐再跑 `.koozee/node_modules/.bin/koozee-cli update`。

## 登录

未登录禁止 Shortcut。先 `koozee-cli auth status`；未登录则走上面的 `auth login`。环境只在登录时写入。凭证在当前项目 `.koozee/credentials.json`。不要提交 `.koozee/`。不要自己打开授权 URL。

## 契约

CLI 不提问。安装、更新、生成不要停下来等人。判断成功用 **`ok == true`**。失败看 `error.hint`。参数看 `koozee-cli <命令> -h`。槽看 `+models`、`skill +get`、`workbench +list`。

禁止手写画布 JSON、禁止传坐标、禁止自己拼 OpenAPI path、禁止自己订 SSE。怎么生成、怎么连线，读 `koozee-canvas`。

对人讲到画布上的卡时，用信封里的 `name`。连线讲 `fromName`、`toName`。扇出讲 `nodeIds[].name`。`name` 是空字符串就说「未命名」。不要把 `nodeId`、`nodeIds[].nodeId`、`from`、`to` 念给人。这些只用于下一条 `--node-id`、`--from`、`--to`。两张卡同名也只讲名字。

工作台独有工具先 `workbench +list` 再 `workbench +run --surface workbench`。用户安装会自己升 CLI 和 skills。不要自己 `npm i`。不要只升一边。要马上对齐再跑 `koozee-cli update`。扣豆前先 `+plan`，把 `totalBeans` 和 `warnings` 讲给人，讲完同一轮继续提交。怎么生成读 `koozee-canvas`。

人要本机副本：用信封 `results[].url` 拉到用户指定的相对目录。不要绝对路径。不要静默覆盖。没有 `+download`。
