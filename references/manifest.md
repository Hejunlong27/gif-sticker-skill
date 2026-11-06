# 图集导出清单格式

准备一个 JSON：标题、精确的表情数量、有序的分类列表。每张图集是透明 PNG，等格排布，一格一个精灵。路径相对清单文件，行列坐标从 0 开始。

```json
{
  "title": "雨天搭档",
  "count": 2,
  "groups": [{
    "name": "日常",
    "atlas": "sample-atlas.png",
    "grid": [2, 1],
    "ink": "#5E4493",
    "tint": "#C8B2F4",
    "background": "#EBE3FA",
    "entries": [
      {"text":"醒醒","cell":[0,0],"motion":"startle","icon":"alert"},
      {"text":"晚安","cell":[1,0],"motion":"breathe","icon":"sleep"}
    ]
  }]
}
```

**间距不均匀的图集**：给条目加可选的 `crop`，填像素坐标 `[left, top, right, bottom]`。选中的区域必须包含完整精灵，并且与相邻精灵之间有透明间隔。渲染前先把裁切区域看一眼。

## 动效预设

`nod`、`jump`、`sway`、`startle`、`tremble`、`ponder`、`breathe`、`stomp`、`rush`、`peek`、`pulse`、`pop`、`offer`、`lean`、`droop`、`dance`

这些预设对精灵做位移、挤压、错切，节奏按"短促反应"设计。**它们不会在一轮循环里生成新的角色姿势**——需要换姿势就走关键帧流程。

## 动效点缀

`spark`、`heart`、`tear`、`check`、`question`、`alert`、`sleep`、`steam`、`note`、`cross`、`bubble`、`dots`、`bolt`、`arrow`、`petal`、`none`

姿势本身信息量已经够了就用 `none`，别叠。

## 命令

```sh
python3 scripts/export_pack.py manifest.json build --font /path/to/font.ttf --preview
```

- 字体是 TTC 时用 `--font-index` 指定字面。
- **输出目录必须是空的**，不能有上一轮的残留。
- 产出：分类 GIF 文件夹、跟着 ZIP 一起走的离线画廊、可选的 MP4。
- 环境吃不住并发就加 `--jobs 2`。
- MP4 需要带 libx264 的 FFmpeg；GIF 导出需要 Pillow。

## 渲染清晰度与预览

导出脚本默认 **hd**，老清单没有渲染字段也一样。它直接从原始裁切以 512 × 512 采样，缩放用 LANCZOS，形变用 BICUBIC。写实人物和宠物**不经过**那个 160 像素的中间画布。

只有确定要像素画时，才在顶层写 `"render_style": "pixel"`。像素模式保留 320 × 320 输出和原来的最近邻管线。命令行 `--render-style hd` / `--render-style pixel` 优先于清单。**模式写错会直接报错，不会静默降级。**

`--preview` 时，HD 默认无标题网格，像素默认带分类标题；`--preview-layout clean` 和 `--preview-layout chapters` 可以显式指定。

源素材偏软、或用户要求清晰度对比时，读[清晰度说明](clarity.md)。两种模式下导出脚本都会校验实际尺寸、透明度、边距和动效。
