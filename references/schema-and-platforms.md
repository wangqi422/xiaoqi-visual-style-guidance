# 风格 JSON Schema 与平台提示词规范

仅在需要结构化 JSON、Nano Banana 或 GPT Image 2 输出时读取本文件。

## 严格 JSON Schema

输出必须是一个有效 JSON 对象。不得添加 Markdown 围栏、注释、尾随逗号或对象外文本。

```json
{
  "style_analysis": {
    "color": {
      "primary_colors": [],
      "secondary_colors": [],
      "color_temperature": "unknown",
      "saturation": "unknown",
      "contrast": "unknown"
    },
    "typography": {
      "status": "present",
      "font_style": "unknown",
      "font_weight": "unknown",
      "line_spacing": "unknown",
      "hierarchy": "unknown"
    },
    "composition": {
      "layout": "unknown",
      "subject_position": "unknown",
      "visual_balance": "unknown",
      "negative_space_ratio": "unknown",
      "depth_layers": "unknown"
    },
    "lighting": {
      "lighting_type": "unknown",
      "key_light_direction": "unknown",
      "shadow_character": "unknown",
      "highlight_position": "unknown",
      "dynamic_range": "unknown"
    },
    "texture_and_material": {
      "surface_textures": [],
      "materials": [],
      "rendering_characteristics": "unknown"
    },
    "atmosphere": {
      "emotional_tone": [],
      "style_tags": [],
      "commercial_positioning": "unknown"
    }
  },
  "reference_roles": [
    {
      "image_id": "image_1",
      "responsibilities": ["style"],
      "priority": 1,
      "do_not_inherit": []
    }
  ],
  "nanobanana_prompt": {
    "language": "zh-CN",
    "prompt": "",
    "negative_constraints": []
  },
  "gpt_image_2_prompt": {
    "language": "en",
    "prompt": "",
    "negative_constraints": []
  },
  "confidence": {
    "color": 0.0,
    "typography": 0.0,
    "composition": 0.0,
    "lighting": 0.0,
    "texture_and_material": 0.0,
    "atmosphere": 0.0
  }
}
```

## 字段规则

- `primary_colors`、`secondary_colors`：写色名并在可可靠估计时附近似 HEX，如 `深青灰 #26373A`。
- `color_temperature`：使用 `warm`、`neutral`、`cool`、`mixed` 或 `unknown`。
- `saturation`、`contrast`：使用可观察的等级和分布，例如 `整体低饱和，橙色警示光局部高饱和`。
- `typography.status`：只允许 `present`、`not_applicable` 或 `unknown`。为 `not_applicable` 时，其余排版字符串同样写 `not_applicable`。
- `negative_space_ratio`：可估计时写比例区间，如 `约 30%–40%`；否则写 `unknown`。
- `reference_roles.priority`：数字越小优先级越高，同一职责不得有两个相同优先级。
- `confidence`：`0.0` 表示没有可靠视觉证据，`1.0` 表示证据非常明确；不得用字符串百分比。
- `prompt`：没有目标主体、目标场景或迁移任务时保持 `""`，不得用占位符代替。

## Nano Banana 提示词

默认使用中文自然语言，并按以下顺序组织成一段完整指令：

1. 任务与最终目标。
2. 每张参考图的唯一职责和优先级。
3. `严格保留`：主体身份、构图、动作、文字或其他不可变内容。
4. `仅替换`：用户明确要求改变的内容。
5. `禁止继承`：各参考图中与职责无关的人物、背景、机位、文字、Logo 或水印。
6. 主体、空间关系、镜头、光线、色彩、材质和画幅。
7. 常见失败模式的简短禁止项。

编辑现有图时使用这种低歧义句式：

```text
以 image_1 作为唯一构图与主体位置参考。仅把目标主体的色彩、光影和材质转换为 image_2 的视觉语言；严格保持 image_1 的摄像机、透视、裁切、动作、轮廓和背景布局不变。不要继承 image_2 的人物、物体、文字、Logo、背景或原始机位。
```

不要只堆叠风格关键词。把“电影感”落实为机位、焦距感、光源方向、光比、景深、色彩分离和材质反射。

## GPT Image 2 提示词

默认使用英文自然语言，生成一个完整而非关键词堆叠的生产提示词。按以下顺序组织：

1. `Objective`：最终要生成或编辑什么。
2. `Reference roles`：逐张绑定职责和优先级。
3. `Preserve` 与 `Change only`：明确不可变内容和唯一允许修改的内容。
4. `Subject and action`：身份、外观、身体朝向、重心、接触或握持关系。
5. `Camera and composition`：机位、视角、焦距感、透视、裁切、主体占比和景深层次。
6. `Environment`：场景资产、空间尺度、天气和叙事元素。
7. `Lighting and color`：主光、辅光、色温、曝光、高光和调色。
8. `Materials and finish`：皮肤、织物、金属、玻璃等具体表面响应。
9. `Quality target`：真实镜头语言、清晰主体、适度背景后退和商业宣传完成度。
10. `Avoid`：仅列与当前任务有关的失败模式。

构图锁定模板：

```text
Use image_1 as the sole composition and camera reference. Preserve its exact camera position, camera height, viewing angle, perspective, subject placement, body orientation, crop, silhouette, foreground occlusion, and foreground–midground–background spacing. Change only the color palette, lighting behavior, surface treatment, and finishing style according to image_2. Do not inherit any people, objects, text, logos, background layout, or camera viewpoint from image_2. Do not redesign the composition.
```

多参考图时使用 `image_1 controls...; image_2 controls only...; image_3 controls only...`，不要写 `blend all references`。

## 常见失败与修正

| 失败 | 修正 |
|---|---|
| 风格图覆盖构图 | 将构图图声明为唯一镜头参考，并禁止继承风格图机位与布局 |
| 人物变成正面摆拍 | 写清胸口、骨盆、背部可见比例和头部回望方向 |
| 动作变普通 | 拆解重心、脊柱、肩胯、手肘、腿部支撑和运动趋势 |
| 武器变小或漂浮 | 写清相对躯干长度、厚度、双手接触点和遮挡关系 |
| 枪械遮挡面部 | 指定枪身高度、枪口方向、头部转向和面部净空区域 |
| 场景带回原机位 | 场景图只提供资产与光照，将场景重投射到构图图镜头 |
| 画面过满 | 降低次要元素对比度，让背景后退并保留明确呼吸区 |
| 输出不是有效 JSON | 只输出一个对象，去掉围栏、注释、标题和尾随逗号 |

