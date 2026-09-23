---
name: koozee-canvas
description: "Generate with Koozee only when the workbench has no such tool: canvas image, video, or text models, prompts, linking cards. Use when the user asks for 图片, 视频, 文本, 提示词 that are not a workbench tool, or explicitly asks for 工作流 or 画布. Read koozee-shared first."
metadata:
  requires:
    bins: ["koozee-cli"]
---

# 画布路由

先读 `koozee-shared`。工作台没有的才走这里。用户明确说工作流或画布时，交叉功能可以留在这里。人在对话里点头之前，不要提交生成。参数看 `koozee-cli <命令> -h`。槽看 `+models` 或 `skill +get`。

扣豆、`--place`、`skill +run --write` 之前先 `+plan --surface workflow|workbench --purpose <短名> --steps <json>`。把 `totalBeans` 和 `warnings` 讲给人。价格表不写在这里。`type: balance` 是余额不足，不要重试提交。

## 怎么选

1. 单模型：`koozee-cli +models`。图 / 视频 / 文本走 `image|video|text +generate`。
2. 工作台没有的 Skill：`koozee-cli skill +list`。槽看 `skill +get --id <id>`，再 `+plan` / `skill +run`。工作台有的或两边都有的改读 `koozee-workbench`。
3. `--surface workflow` 才能落项目。`workbench` 只提交，不落卡、不加线。

## 本地文件

`+upload --file <path> --surface workflow [--project-id <id>]`。`--file` 可重复，多文件横排落 Out。不加边。再 `+link`。`workbench` 只信封。

## 要入线

1. `+generate --id <model> --surface workflow --place [--project-id <id>]` 只落卡，不提交。
2. `+link --project-id <id> --from <source> --to <target>` 一次一条。可再加。不提交。
3. `project +get --project-id <id>` 读节点和入线，讲给人听。
4. 人点头后：`+generate --id <model> --surface workflow --project-id <id> --node-id <id>`。文本当场写入。图 / 视频只提交。不加边。

`+generate --write` 不要用，改 `--place`。`skill +run --write` 才是提交后再写入项目，默认不加。

## 结果

- 文本：信封 `text` 讲给人。`workflow --node-id` 同时写入该卡。人刷新画布才能看见。`taskIds` 为空就不要 `+await`。
- 图 / 视频：信封有 `taskIds` 再 `+await --task-id <id> --surface workflow --project-id <id> --node-id <id>`。成功后写入该卡。超时跑信封 `next_command`。人刷新才能看见。
- Skill：信封有 `taskIds` 用 `+await --task-id`；有 `runId` 用 `+await --run-id --id`。成功后扇出结果卡并加出边。不要对结果卡逐条 `+link`。正文在 `results[].text`。超时跑信封 `next_command`。人刷新才能看见。
