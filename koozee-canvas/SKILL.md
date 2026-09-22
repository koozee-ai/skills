---
name: koozee-canvas
description: "Route canvas work: pick a model or published skill, place a card, link edges, confirm with the user, then generate. Read koozee-shared first."
metadata:
  requires:
    bins: ["koozee-cli"]
---

# 画布路由

先读 `koozee-shared`。人在对话里点头之前，不要提交生成。

扣豆、`--place`、`skill +run --write` 之前先 `+plan --surface workflow|workbench --purpose <短名> --steps <json>`。把 `totalBeans` 和 `warnings` 讲给人。价格表不写在这里。`type: balance` 是余额不足，不要重试提交。

## 怎么选

1. 单模型：`koozee-cli +models`。图 / 视频 / 文本走 `image|video|text +generate`。
2. 四产品或已上线 Skill：`koozee-cli skill +list`。需要输出槽或默认值时 `skill +get --id <id>`，再 `+plan` / `skill +run`。
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
- Skill / 产品：`+await --run-id <id> --id <skillId> --project-id <id> --node-id <任务卡>`，或产品 `+await --task-id <id> --surface workflow --project-id <id> --node-id <任务卡>`。成功后扇出结果卡并加出边。不要对结果卡逐条 `+link`。正文在 `results[].text`。超时跑信封 `next_command`。人刷新才能看见。
