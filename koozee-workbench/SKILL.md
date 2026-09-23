---
name: koozee-workbench
description: "Run Koozee workbench tools, including 标准平铺, 服装试穿, Sexy服装试穿, 换姿势, 服装换色, 详情页. 比基尼试穿, 泳衣试穿, 内衣试穿 use Sexy服装试穿. Also 平铺转3D, 提取主图, 提取印花, 换印花, 细节图, 鞋子提取, 商品换场景, 换模特, 换角度, 换场景, 鞋子试穿, 画质修复, 视频高清, AI消除, AI抠图, Tiktok定点自拍, 穿搭卡点素材, 服装线稿精绘, 鞋子线稿精绘, 服装去褶皱, 衣服色彩迁移. Use workflow only when the user explicitly asks for 工作流 or 画布. Read koozee-shared first."
metadata:
  requires:
    bins: ["koozee-cli"]
---

# 工作台

先读 `koozee-shared`。工作台有的，或和工作流交叉的，走这里，`--surface workbench`。工作台没有的改读 `koozee-canvas`。用户明确说工作流或画布时，交叉功能可以改走 canvas。

## 功能命中

用户的说法对到工具名再用。名字对不上，用下面的别名。没对上改走 `koozee-canvas`。

衣服：标准平铺、平铺转3D、提取主图、服装换色、提取印花、换印花、细节图、鞋子提取、商品换场景。

模特：服装试穿、Sexy服装试穿、换姿势、换模特、换角度、换场景、鞋子试穿。

设计：详情页、画质修复、视频高清、AI抠图、AI消除。

还有：Tiktok定点自拍、穿搭卡点素材、服装线稿精绘、鞋子线稿精绘、服装去褶皱、衣服色彩迁移。

别名：

- 比基尼试穿、泳衣试穿、内衣试穿 → Sexy服装试穿
- 电商详情页 → 详情页
- 去除背景、去背景、抠图 → AI抠图
- 服装线稿转3D → 服装线稿精绘
- 定点自拍 → Tiktok定点自拍
- 穿搭卡点 → 穿搭卡点素材
- 模特换姿势 → 换姿势
- 模特换模特 → 换模特
- 模特换角度 → 换角度
- 模特换场景 → 换场景

不命中：视频复刻、动态视频、口播视频、海报设计。

标准平铺、服装试穿、Sexy服装试穿、换姿势、服装换色：`skill +list`，槽看 `skill +get`，再 `skill +run --surface workbench`。详情页：`image +generate --surface workbench`。其余：`workbench +list` 再 `workbench +run --id <id> --surface workbench --input <json>`。不要 `--write`。结果用 `+await --task-id --surface workbench`。参数看 `koozee-cli <命令> -h`。槽在 `skill +get` 或 `workbench +list`。
