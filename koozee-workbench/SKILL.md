---
name: koozee-workbench
description: "Route workbench-only tools. Read koozee-shared first. Canvas models and published skills still use koozee-canvas."
metadata:
  requires:
    bins: ["koozee-cli"]
---

# 工作台独有

先读 `koozee-shared`。四产品 / CMS Skill / 画布模型换 `--surface workbench` 仍走 `koozee-canvas`。

独有页：先 `koozee-cli workbench +list`。人点头后 `workbench +run --id <id> --surface workbench --input <json>`。不要 `workflow`。不要 `--write`。结果用 `+await --task-id --surface workbench`。不要跑视频复刻。
