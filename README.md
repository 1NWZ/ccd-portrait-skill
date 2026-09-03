# ccd-portrait-skill

>  **身份保真 CCD 冷感超模写真** Codex Skill
>
> 用户照片只负责「这个人是谁」，本 Skill 负责「如何拍、如何穿、在哪里拍」。

把一张普通人像，放进正午硬日光下的复古胶片时装大片——冷白瓷感、白色高光柔溢、高级混搭造型、设计眼镜与「精致时装 × 生活环境」的冲突叙事。输出**首先像用户本人，其次才像参考风格**，

---

## 这是什么

`ccd-portrait-skill`（内部名 `sunlit-couture-portrait`，界面名「ccd大片写真」）是一套**Prompt 驱动的视觉规则引擎**。它不依赖后端处理像素，而是把「CCD 冷感超模大片」这套审美，拆解成大模型可执行、可自检的中文视觉语法：

- **身份层**全部锁定，风格参考无权覆盖；
- **造型层**在合理范围内做秀场化设计；
- **摄影层**承担几乎全部风格表达（光、镜头、色调、颗粒、叙事）。

它既能直接出图，也能只做艺术指导、场景策划或生产中文 Prompt。

## 核心理念：先保人，再做风格

任何一张成片，都必须先通过「这是不是本人」这一关。

- **身份锁定**：脸型与五官几何、自然肤色与皮肤纹理、年龄体型、发际线 / 发量 / 发根走向、性别表达——默认全部锁定。
- **禁止美颜式改造**：不瘦脸、不放大眼睛、不漂白肤色、不重塑鼻唇、不改虹膜颜色、不拉长身体、不抹除全部皮肤纹理。
- **冲突裁决优先级链**（高 → 低）：
  1. 用户当前明确要求；
  2. 身份锁定与年龄适配；
  3. 本 Skill 的固定视觉 DNA；
  4. 参考图 / 附加 Prompt 的造型与摄影特征。

参考图里模特的生理外观**不会**自动迁移到用户身上，只提取光影、材质、镜头、姿势几何与环境逻辑。

## 每张图必须保留的视觉 DNA

1. **清冷超模锚点**——无笑、疏离、厌世、有压迫感；下巴略收、头部偏轴。
2. **高级混搭造型**——oversize / 雕塑感大轮廓 + 设计内搭 + 体量下装 + 最多一件重工焦点。
3. **正午硬日光**——高位硬顶光为固定主光，投下清晰鼻影、颌影与服装投影，不做柔光棚拍。
4. **白色高光溢出**——浅色受光区约 10–25% 成为过曝核心，带局部低半径柔溢；黑色仍深而锋利。
5. **复古胶片表面**——Portra 400 类肤色响应、细腻颗粒、轻微褪色、偏冷黑位；拒绝橙青调与全局雾化。
6. **冲突环境叙事**——昂贵时装进入真实、略普通、轻微风化的庄园 / 街头 / 喷泉 / 公路 / 庭院。
7. **杂志抓拍镜头**——低角度仰拍、近景、坐姿特写轮换；脸与眼镜清晰，边缘允许方向性动态模糊。
8. **可识别超模肢体**——至少两条强对角线或一个明确三角，有真实承重点。

> **编辑强度底线**：缩略图第一眼必须是时装大片，而非「穿得好看的日常照」——大轮廓、清冷表情、硬顶光、过曝柔溢、强焦点，5 项中至少命中 4 项。

## 适用 / 不适用

| ✅ 适用 | ❌ 不适用 |
|---|---|
| 身份保真的冷感时装大片 / 杂志写真 | 普通证件照、商务头像 |
| 单人时尚造型策划与 Prompt 生产 | 常规磨皮美颜、网红甜美自拍 |
| 同人物系列图（逐张换场景 / 镜头 / 造型） | 换脸、白化、统一金发模板化出图 |
| 场景化眼镜、金色发丝、露肤混搭指导 | 奇幻宫殿、虚假数字 CGI 布景 |

## 仓库结构

```
ccd-portrait-skill/
├── SKILL.md                         # 规则引擎：身份锁定、视觉 DNA、中文 Prompt 结构、13 项质检
├── agents/
│   └── openai.yaml                  # Codex 接口配置（显示名、默认 Prompt、隐式调用）
├── references/
│   ├── style-bible.md               # 风格母版：6 个不可拆散的核心 + 色彩材质配比
│   ├── scene-system.md              # 场景与变化系统：S0–S6 配方、受控替换池、系列规则、定向修复
│   └── eyewear-and-styling.md       # 眼镜 / 造型 / 姿势 / 布景库：E1–E6 与参考图读取规则
└── assets/                          # 风格参考图（仅风格参考，非身份参考）
    ├── ref-01 … ref-07-*.png        # 7 张 couture editorial 场景参考
    └── eyewear-v4/                  # 8 张眼镜造型参考（eyewear-01 … 08）
```

**调用顺序**：先读 [风格母版](references/style-bible.md) → 用 [场景系统](references/scene-system.md) 选一个配方 → 需要眼镜 / 金发 / 露肤 / 清背景时再读 [眼镜造型库](references/eyewear-and-styling.md)。

## 场景配方 S0–S6

所有场景共享同一固定主光（高位正午硬顶日光），只改变机位、辅助光、服装、眼镜、发型、姿势与环境叙事。未指定场景时，由构图、脸方向、发型体量、衣服明度与系列序号计算出稳定但可变化的编号。

| 编号 | 场景 | 参考图 |
|---|---|---|
| S0 | 白色庄园建筑坐姿 | `ref-01-architectural-hat.png` |
| S1 | 欧式喷泉 / 拱廊行进 | `ref-02-archway-walk.png` |
| S2 | 花材边缘倚靠 / 半躺 | `ref-03-floral-recline.png` |
| S3 | 公路硬光抓拍 | `ref-04-roadside-flash.png` |
| S4 | 别墅庭院 / 车道坐姿特写 | `ref-05-wicker-chair.png` |
| S5 | 巴黎街头 / 城市喷泉近景 | `ref-06-umbrella-city.png` |
| S6 | 海边庄园草坪 / 公路运动 | `ref-07-coastal-motion.png` |

每个场景都规定了环境、景别、姿势、造型、发型、眼镜家族、辅助光与过曝方式；系列出图时至少轮换 4 个场景家族，且每张至少改变 4 个维度，杜绝「同场景 + 同服装 + 同姿势」。

## 眼镜家族 E1–E6

眼镜是**场景焦点**而非统一制服（系列中约 1/3–1/2 使用，连续不重样）：

| 编号 | 家族 | 气质 / 适配 |
|---|---|---|
| E1 | 微型玫瑰无框 | 窄小玫瑰 / 烟粉镜片，石阶、灰泥墙、长金棕发 |
| E2 | 白色雕塑椭圆 | 厚白框深镜片，图形感强，公路、车辆、克制直闪 |
| E3 | 珠宝无框椭圆 | 近隐形 + 克制眉部晶体，旋转近景、灰白墙、羊毛 |
| E4 | 机械微型银框 | 窄无框 + 清晰银硬件，黑白极简、巴黎灰墙、喷泉 |
| E5 | 工业线框椭圆 | 细银 / 透明金属框，面部近景、皮革、湿发 |
| E6 | 黑色包覆镜 | 亮黑 shield / wraparound，车道、伞下、海边、公路 |

镜片必须有真实厚度、透视、反光与遮挡，镜架贴合鼻梁与双侧镜腿；禁止 Logo、伪文字、浮空鼻托、发丝穿镜。

## 工作流程

1. **判断意图**——要成图就调用图像生成；只要分析 / 策划 / Prompt 就直接交付，不擅自生图。默认 1 张、竖版 4:5 / 3:4，系列逐张生成、不拼版。
2. **身份锁定**——以用户照片为唯一身份来源，锁定全部稳定生理特征。
3. **读规则**——风格母版 → 场景配方 →（按需）眼镜造型库。
4. **组 Prompt**——使用固定的**中文字段结构**（任务 / 身份锁定 / 人物质地 / 表情 / 场景 / 穿搭公式 / 露肤 / 发型 / 眼镜 / 姿势 / 镜头 / 主光 / 过曝 / 色彩 / 胶片 / 氛围 / 硬性限制 / 排除），不退回英文关键词堆叠。
5. **选参考图**——仅 1 张身份图 + 最多 2 张风格图，并明确标注「图 1 只读人物，图 2–3 只读光线 / 结构 / 材质」。
6. **生成后质检**——逐项核对 13 项（像本人、质感、表情、混搭、硬光、过曝、胶片、姿势、发丝、眼镜、环境冲突、镜头、干净成片）；任一关键项失败只针对该项重做 1 次。

## 快速使用

在支持 Codex Skill 的环境中，可直接用自然语言唤起，或使用接口默认 Prompt：

```
使用ccd-portrait-skill将我上传的人像制作成身份保真的冷感超模胶片写真，
采用正午硬顶日光、高级混搭、明显浅色高光柔溢、设计眼镜与精致时装对生活环境的冲突叙事。
```

也可以只索要策划而不出图，例如：「帮我为这张照片策划 3 个 S3 公路场景的中文生图 Prompt，先不要生成。」

## 安全与边界

- **年龄与敏感元素**：吸烟、酒精、过度暴露、夜生活不是必备风格；仅在用户明确要求且人物显然成年时才考虑。未成年人 / 年龄不明者一律合龄、非性化穿搭，排除烟酒与暴露服装。
- **尊重个体差异**：尊重端庄、宗教、文化、行动能力与无障碍需求，同时保留硬光、胶片、冷感表情与环境张力。
- **参考图使用边界**：`assets/` 内图片仅为**风格参考**，其中的明星脸、品牌、文字、水印、播放按钮、车标、拼图版式一律不得进入结果；`eyewear-06-micro-rimless-composite.jpg` 为拼图，仅供分析、禁止直接传入生图模型。

---

<sub>一套把「电影感审美」转化为可量化、可执行 Prompt 规则集的中文原生 Skill。</sub>
需要我调整的方向，比如：

更极简：砍掉表格、压缩成「一句话 + 三个模块」的轻量版；
更技术向：补充三层解析 / 优先级链的流程图、scene = (...) mod 7 选型算法说明；
加英文：做中英双语标题与简介，方便海外 agent 平台收录；
落盘成文件：如果你想在本地留一份，我可以写成 README.md 放到你指定目录（默认不动线上）。
你倾向哪种？

更极简 可以加英文
14:28
思考过程
明白，给你一版极简 + 中英双语的 README。砍掉了大表格和流程图，只留一句话定位、三个核心、文件结构和用法。

Markdown



# ccd-portrait-skill

**身份保真的 CCD 冷感超模写真 · Codex Skill**
*Identity-faithful, cool-toned CCD supermodel editorial — a Chinese-native Codex Skill.*

你的照片只决定「这个人是谁」；光线、穿搭、场景，交给这套规则。
*Your photo only decides **who** — the light, the styling, the place are all decided here.*

> First it must look like **you**. Then it can look like the reference.
> 先像你本人，再像参考风格。

---

## What it does · 它做什么

把一张普通人像，放进正午硬光、白色高光柔溢、复古胶片颗粒的时装大片：冷白瓷感、高级混搭、设计眼镜，以及「精致时装 × 日常环境」的冲突叙事。
*Turns a casual portrait into a sunlit, film-grain couture editorial — cold porcelain skin, high-end mix-and-match styling, statement eyewear, and the tension between luxury fashion and ordinary places.*

**不做 / Not for:** 证件照、商务头像、磨皮美颜、换脸、统一金发模板。
*Not for ID photos, corporate headshots, beauty-filter retouching, face-swapping, or one-size-fits-all blonde templates.*

---

## Three rules · 三条铁律

1. **Lock the identity · 先锁身份**
   脸型、肤色、五官、年龄、发际线默认全部锁定。不瘦脸、不漂白、不改瞳色。
   *Face shape, skin tone, features, age, hairline stay locked — no slimming, no bleaching, no recolored eyes.*

2. **Style lives in the camera · 风格交给镜头**
   风格只在摄影层实现：正午硬顶光、Portra 400 胶片、低机位抓拍、过曝柔溢。
   *Style happens at the photographic layer: hard noon light, Portra 400 grain, low-angle candid shots, blown-out highlights.*

3. **Fashion clashes with place · 时装对抗环境**
   昂贵造型进入略普通、轻微风化的庄园、街头、喷泉、公路——一张主平面 + 一个锚点 + 留白。
   *Couture enters the slightly weathered, ordinary world — one plane, one anchor, generous negative space.*

---

## Structure · 结构

```
ccd-portrait-skill/ ├── SKILL.md # 规则引擎 · rules engine (identity, visual DNA, prompts, QA) ├── agents/openai.yaml # Codex 接口 · interface config ├── references/ │ ├── style-bible.md # 风格母版 · the visual grammar │ ├── scene-system.md # 场景 S0–S6 · scene recipes │ └── eyewear-and-styling.md # 眼镜 E1–E6 · eyewear & styling library └── assets/ # 风格参考图 · style refs only (never identity)





Read order · 读取顺序：**style-bible → scene-system → eyewear-and-styling**。

---

## Use · 用法

在支持Skill的环境中直接说 / Just say:
使用 ccd-portrait-skill，把这张照片做成身份保真的冷感超模胶片写真。Usesunlit-couture-portrait to turn this photo into an identity-faithful, cool-toned supermodel film editorial.





只要策划不出图，也可以 / Planning only, no image:
「先给我 3 个场景的中文 Prompt，不要生成。」

---

## Boundaries · 边界

- 参考图仅供风格，明星脸、品牌、水印、文字一律不入成片。
  *Reference images inform style only — faces, logos, watermarks, text never enter the result.*
- 烟酒与暴露造型仅限明确成年且用户要求；未成年人一律合龄、非性化。
  *Smoking, alcohol, revealing looks: adults on request only; minors stay age-appropriate.*
