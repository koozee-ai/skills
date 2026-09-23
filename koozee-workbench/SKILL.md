---
name: koozee-workbench
description: "Route workbench-only tools. Read koozee-shared first. Canvas models and skills still use koozee-canvas."
metadata:
  requires:
    bins: ["koozee-cli"]
---

# 工作台独有

先读 `koozee-shared`。Skill 和画布模型换 `--surface workbench` 仍走 `koozee-canvas`。

独有页：先 `koozee-cli workbench +list`。人点头后 `workbench +run --id <id> --surface workbench --input <json>`。不要 `workflow`。不要 `--write`。结果用 `+await --task-id --surface workbench`。不要跑视频复刻。参数看 `workbench +run -h`。槽在 `workbench +list`。
