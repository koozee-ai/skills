---
name: koozee-canvas
description: "Generate with Koozee only when the workbench has no such tool: canvas image, video, or text models, prompts, linking cards. Use when the user asks for 图片, 视频, 文本, 提示词, 服装, 电商, 营销 that are not a workbench tool. Read koozee-shared first."
---

# 画布路由

先读 `koozee-shared`。工作台没有的才走这里。提交生成不要停下来等人。参数看 `koozee-cli <命令> -h`。槽看 `+models` 或 `skill +get`。

扣豆、`--place`、`skill +run --write` 之前先 `+plan --surface workflow|workbench --purpose <短名> --steps <json>`。把 `totalBeans` 和 `warnings` 讲给人，讲完同一轮继续提交。价格表不写在这里。`type: balance` 是余额不足，不要重试提交。

## 怎么选

1. 自由图 / 视频 / 文本：`koozee-cli +models`，再 `image|video|text +generate --surface workflow`。先 `--place` 落卡，同一轮 `--node-id` 提交。图 / 视频再 `+await --surface workflow --project-id <id> --node-id <id>` 写入。把信封里的项目地址讲给人。不要 `--surface workbench`。
2. 工作台没有的 Skill：`koozee-cli skill +list`。槽看 `skill +get --id <id>`，再 `+plan` / `skill +run --surface workflow`。要进项目再加 `--write`。工作台工具改读 `koozee-workbench`。
3. 这里只有 `--surface workflow` 才落项目。工作台工具不在这里。

## 本地文件

`+upload --file <path> --surface workflow [--project-id <id>]`。`--file` 可重复，多文件横排落 Out。不加边。再 `+link`。`workbench` 只信封。

## 要入线

1. 后继和边同一笔：`+generate --id <model> --surface workflow --place --from <source> --project-id <id>`。第一次出现就在源右侧。没有入线源的新卡仍 `--place`，落在下一行左缘。
2. 已经在图上的卡才 `+link --project-id <id> --from <source> --to <target>`，一次一条。页开着会等展示框量完再把新卡移到画面中间；坐标又变了再居中一次。
3. `project +get --project-id <id>` 读节点和入线。对人讲每张卡的 `name`，边讲 `fromName` → `toName`。不要念 id。
4. 同一轮接着：`+generate --id <model> --surface workflow --project-id <id> --node-id <id>`。文本当场写入。图 / 视频只提交。不加边。

`+generate --write` 不要用，改 `--place`。`skill +run --write` 才是提交后再写入项目，默认不加。

## 结果

对人讲卡时用信封 `name`。不要念 `nodeId`。

- 文本：信封 `text` 讲给人。`workflow --node-id` 同时写入该卡。命令会打开项目页；页开着就能看见节点和「Agent操作中」，不必再刷新。`taskIds` 为空就不要 `+await`。
- 图 / 视频：信封有 `taskIds` 再 `+await --task-id <id> --surface workflow --project-id <id> --node-id <id>`。成功后写入该卡。超时跑信封 `next_command`。命令会打开项目页；页开着就能看见节点和「Agent操作中」，不必再刷新。
- Skill：信封有 `taskIds` 用 `+await --task-id`；有 `runId` 用 `+await --run-id --id`。成功后扇出结果卡并加出边。不要对结果卡逐条 `+link`。正文在 `results[].text`。超时跑信封 `next_command`。命令会打开项目页；页开着就能看见节点和「Agent操作中」，不必再刷新。
