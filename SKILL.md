# kathy-xhs-images Skill

> 小红书图片生成（ImageGen 直接调用流程）
> 版本：v3.0
> 创建日期：2026-04-04
> 更新日期：2026-04-06

## 概述

封装 Kathy 在小红书图片生成中积累的 ImageGen 直接调用流程。支持**风格 × 画布**二维系统，适用于需要完全控制 prompt 内容、精细调整每个视觉元素、或 baoyu skill 生成效果不满意的场景。

**触发条件：** 用户选择"方式 B：直接调用 ImageGen"，或在 Step D 中明确要求手动编写 prompt。

## 风格 × 画布系统

### 风格 Gallery

| 风格标识 | 名称 | 描述 | 状态 |
|----------|------|------|------|
| `pencil-handdrawn` | 铅笔手绘风 | Notion风格铅笔线条，蓝主色+橙紫点缀 | 默认 |
| `minimal-warm` | 极简暖色风 | 暖白背景+橙色编号/高亮，极简现代 | 新增 |

**风格定义详见：**
- `references/styles/pencil-handdrawn.md`
- `references/styles/minimal-warm.md`

未来可扩展：水彩风、扁平风、复古风等。

### 画布 Gallery

| 画布标识 | 名称 | 宽高比 | 描述 | 状态 |
|----------|------|--------|------|------|
| `grid-canvas` | 网格画布 | 3:4 | 小红书标准竖版，模块化网格布局 | 默认 |
| `solid-canvas` | 纯色画布 | 3:4 | 纯色背景，无网格/纹理 | 新增 |

**画布定义详见：**
- `references/canvases/grid-canvas.md`
- `references/canvases/solid-canvas.md`

未来可扩展：全屏画布、分屏画布、手机外框画布等。

## 核心工作流

### Step 0：页面类型判断（自动）

**自动判断页面类型，选择参考案例：**

| 类型 | 判断依据 | 参考案例目录 | 生成策略 |
|------|---------|-------------|---------|
| **Cover** | C2 中 P0 大字报封面 | `knowledge/cases/good/cover/` | 纯色背景+超大标题+关键词高亮，极简 |
| **Inner** | C2 中 P1-Pn 内容页 | `knowledge/cases/good/inner/` | 模块化布局+图标辅助+信息密度高 |

**输出：** 确定页面类型后，自动读取对应参考案例的 prompt.md 作为编写参考。

### Step 1：风格与画布选择（自动）

**当前默认配置：**
- 风格：`pencil-handdrawn`（铅笔手绘风）
- 画布：`grid-canvas`（网格画布）

**自动注入风格约束：** 从 `references/styles/pencil-handdrawn.md` 中提取色彩、线条、约束模板、点缀模板。
**自动注入画布约束：** 从 `references/canvases/grid-canvas.md` 中提取宽高比、安全区域。

**未来用户指定风格/画布时**，替换为对应定义文件中的参数。

### Step 2：页面布局选择（自动判断 + 复杂页面确认）

**第一步：根据页面内容特征自动选择布局模式**

| 布局 | 特征判断 | 适用场景 |
|------|---------|---------|
| **布局 A：浏览器预览 + 功能清单** | 页面需要展示网页/软件界面 + 功能列表 | 任务介绍、需求说明、最终效果展示 |
| **布局 B：手机双屏对比** | 页面需要展示"输入→输出"、"修改前→修改后"的对比流程 | 发送指令→AI生成、效果对比 |
| **布局 C：操作步骤时间线** | 页面包含 2 个及以上连续操作步骤 | 跟着做系列、多步教程 |
| **布局 D：多方案卡片** | 页面需要展示多种解决方案 | 问题解决思路、调试步骤 |
| **布局 E：问题调试页** | 页面需要展示问题场景 + 解决方法 | 问题调试页、错误修复指导 |
| **布局 F：多宫格展示** | 页面需要展示多个状态/版本的效果对比 + 功能清单 | 最终效果展示、多版本对比 |
| **布局 G：多卡片竖向排列** | 页面需要展示多个指令/方法/清单 | 多指令展示、多方法介绍、清单类内容 |
| **布局 H：技能排行榜/列表页** | 页面需要展示技能/工具排行榜、单个技能详情 | CLAWHUB技能介绍、TOP N列表 |
| **布局 H-2：技能总览列表页** | 页面需要展示排行榜总结、一图看懂 | 总结页、排行榜总览 |
| **布局 I：封面页** | 系列帖封面、主题介绍页、吸引点击的首页 | 封面页、吸引点击的首页 |

**第二步：复杂页面先确认布局结构（硬性规则）**

当页面无法明确匹配上述布局，或包含 2 个及以上独立视觉区域但布局模式不清晰时，必须先用文字描述页面的布局结构和每个区域的表达方式，与用户确认后再编写 Prompt。

简单页面（单一主题、单一视觉元素，且明确匹配布局 A/B/C/D/E）可以直接进入 Step 3 编写 Prompt。

### Step 3：编写 Prompt

基于 C2 内容、已选风格/画布/布局和参考案例编写完整 prompt。

**风格注入：** 详见 `references/styles/pencil-handdrawn.md`
- 色彩系统（主色、点缀色、强调色、功能色）
- 线条特征
- Typography 特征
- 约束模板、点缀模板

**画布注入：** 详见 `references/canvases/grid-canvas.md`
- 宽高比、安全区域

**布局模板：** 详见 `references/layouts/layout-patterns.md`
- 布局 A~E 模板及适用场景
- 每个布局包含风格无关的结构模板 + 铅笔手绘风示例

**编写指南：** 详见 `references/workflows/prompt-writing-guide.md`
- 风格注入流程（编写 Prompt 前必做）
- 三段式结构（标题 + 区域描述 + 风格约束）
- 标题规范（实战案例前缀、超大号、对比正文字号）
- 内容撑满技巧
- 手绘元素分类（有意义 vs 无意义）

**图标搭配：** 详见 `references/elements/icons.md`
**点缀技巧：** 详见 `references/elements/decorations.md`

### Step 4：调用 ImageGen

1. 生图前清理 vibe_images/：`cd /Users/kathyzhang/Documents/vibe_images && rm -f P*.png 2>/dev/null`
2. 调用 ImageGen 生成图片
3. 图片保存到 vibe_images/ 目录

### Step 5：Pipeline 处理

运行 template_pipeline.py 合成网格背景：

```bash
cd /Users/kathyzhang/Documents/kz-xhs-AIagent && python3 xhs-AIcontents/scripts/template_pipeline.py \
  --input-dir /Users/kathyzhang/Documents/vibe_images/ \
  --output-dir "/Users/kathyzhang/Documents/kz-xhs-AIagent/xhs-AIcontents/xhs-post/{post-path}/temporary/P{N}-{主题}" \
  --total-pages {总页数} \
  --series-name "{系列名}" \
  --pages {当前页码} \
  --no-badges
```

**说明：**
- `--no-badges` 为默认参数（徽章已废弃）
- Pipeline 自动扫描 output_dir 中已有最高版本号并 +1
- 处理后图片存入 `temporary/P{N}-{主题}/`

### Step 6：生成后确认流程（硬性规则）

1. **报告存放位置：** 明确告知用户图片已存入哪个页面文件夹及版本号
2. **请求确认：** 询问用户"是否将此图片复制到 final version/？"
3. **等待确认：** 必须等待用户确认后才执行复制

### Step 7：反馈闭环（硬性规则）

当用户给出修改意见或否决方案时：
1. 将用户意见追加到 `memory/feedback-log.md`
2. 如果用户确认了某个设计方向，追加到 `memory/decisions-log.md`
3. 同类反馈出现 2 次及以上时，提取为规则写入 workflow 或 agent 配置

### Step 8：案例沉淀（硬性规则）

**每当用户确认某个生成结果为"好"或"坏"时，必须执行：**

1. **保存图片** → `knowledge/cases/good/inner/{案例名}/image.png` 或 `knowledge/cases/bad/inner/{案例名}/image.png`
2. **保存 Prompt** → 同一文件夹下 `prompt.md`，记录完整 ImageGen prompt
3. **填写元数据** → 同一文件夹下 `metadata.md`，记录来源、评价、关键要素、使用的风格/画布/布局
4. **更新 cases.md** → 添加新案例索引记录

**文件夹结构：**
```
knowledge/cases/
├── good/
│   ├── cover/          # 封面案例
│   │   └── IMG_5031/
│   │       ├── image.png
│   │       ├── prompt.md
│   │       └── metadata.md
│   └── inner/          # 内页案例
│       └── P2-豆包/
│           ├── image.png
│           ├── prompt.md
│           └── metadata.md
└── bad/
    └── inner/          # 坏案例都属于内页
        └── P3-豆包-V1/
            ├── image.png
            ├── prompt.md
            └── metadata.md
```

### Step 9：每日经验总结（硬性规则）

**每日工作结束前，必须执行：**

1. **回顾今日 good/bad cases** → 读取 `knowledge/cases/` 中今日新增的案例
2. **提炼成功模式** → 从 good cases 中提取可复用的 prompt 写法、布局模式、点缀技巧
3. **提炼失败教训** → 从 bad cases 中提取常见错误、避坑指南
4. **更新 Skill** → 将经验沉淀到本 skill 的相应章节
5. **更新风格/布局定义** → 如有新的风格或布局类型，创建或更新相应参考文件

**经验总结格式：**
```markdown
## [日期] 经验总结

### 新增 Good Cases
- [案例名]：[关键成功要素]

### 新增 Bad Cases
- [案例名]：[失败原因]

### 新提炼的 Prompt 模式
- [模式名称]：[描述]

### 更新的 Skill 章节
- [章节名]：[更新内容]
```

## 参考文件索引

| 参考文件 | 内容 |
|---------|------|
| `references/styles/pencil-handdrawn.md` | 铅笔手绘风定义（色彩、线条、约束模板、点缀模板） |
| `references/styles/minimal-warm.md` | 极简暖色风定义（暖白背景、橙色编号/高亮） |
| `references/canvases/grid-canvas.md` | 网格画布定义（宽高比、安全区域、布局适配） |
| `references/canvases/solid-canvas.md` | 纯色画布定义（纯色背景、安全区域） |
| `references/generic-design-rules.md` | **通用设计规则库**（同行对齐、长文本处理、Prompt结构化、Evaluator检查清单） |
| `references/workflows/prompt-writing-guide.md` | 风格注入流程、三段式结构、标题规范、元素分类、常见错误 |
| `references/layouts/layout-patterns.md` | 布局 A~I 风格无关模板 + 铅笔手绘风/minimal-warm示例 |
| `references/elements/icons.md` | 常用图标 Prompt 描述 |
| `references/elements/decorations.md` | 点缀技巧、已验证组合、常见错误 |
| `references/examples/prompt-examples.md` | 已验证成功的完整 Prompt 示例 |

## 文件管理规范

1. **vibe_images/：** 仅存放 ImageGen 原始输出，生图前清理旧文件
2. **temporary/：** 按页面分文件夹 `P{N}-{主题}/`，保留所有历史版本
3. **final version/：** 仅存用户确认的最终成品，文件名不带版本号
4. **严禁删除 temporary/ 中的历史版本**
5. **Cases 管理：** 每个案例独立文件夹，包含 image.png、prompt.md、metadata.md

## 参考案例目录

- Cover 参考：`knowledge/cases/good/cover/`
- Inner 参考：`knowledge/cases/good/inner/`
- Bad 案例：`knowledge/cases/bad/inner/`
- Prompt 模板：`knowledge/prompt-templates/inner-prompts/`

## 逐页等待规则（硬性规则）

每完成一页（生图 + pipeline 合成）后，必须停下来等待用户的下一步指令。绝不自行继续生成下一页。

---

## 经验总结

### 2026-04-05 经验总结

#### 新增 Good Cases
- **P0-豆包指令大全（Cover）**：淡紫色纯色背景 + 黑色粗体标题 + 黄色记号笔高亮"豆包大神" + 手绘小图标点缀（星星+爱心+闪电）
- **P1-P5（Inner）**：统一的多卡片竖向排列布局（布局 G），4-5个卡片，蓝色手绘边框 + 编号圆圈 + 手绘图标 + 输入说明 + 绿色效果文字

#### 新提炼的 Prompt 模式
- **多卡片竖向排列（布局 G）**：Post 07 P1-P5 验证成功，适用于多指令/多方法/清单类内容
  - 关键：编号圆圈必须明确描述"蓝色粗边框圆圈，白色填充，中间黑色数字X，直径约30px"
  - 关键：每个卡片必须用边框容器包裹，否则内容散乱
  - 关键：卡片间用虚线连接营造引导感
  - 关键：点缀元素分散分布，不要集中在一个位置

#### 更新的 Skill 章节
- `references/layouts/layout-patterns.md`：新增布局 G（多卡片竖向排列），含 Post 07 P1-P5 验证案例
- `SKILL.md`：Step 2 布局表增加布局 G，版本更新至 v2.2
- `knowledge/cases/good/`：新增 6 个案例（1 cover + 5 inner）

### 2026-04-06 经验总结

#### 新增风格/画布/布局
- **minimal-warm 极简暖色风**：暖白背景（#f5f0e8）+ 橙色圆形编号（#ff8c42）+ 橙色高亮，适用于CLAWHUB技能介绍类帖子
- **solid-canvas 纯色画布**：3:4 小红书标准竖版，纯色背景无网格/纹理，顶部50px安全区，左右40px，底部60px
- **布局 H：技能排行榜/列表页**：单个技能详情页模板，包含编号圆圈+技能名+描述+核心能力+底部统计标签
- **布局 H-2：技能总览列表页**：总结页/一图看懂模板，包含主标题+7个技能紧凑列表+底部CTA
- **布局 I：封面页**：系列帖封面模板，主标题超大字号+副标题+装饰元素+底部标签

#### 新提炼的 Prompt 模式
- **CLAWHUB 技能介绍体系**：3种布局组合使用
  - 封面（布局 I）：吸引点击，超大标题+装饰
  - 单个技能详情（布局 H）：橙色圆形编号醒目，核心能力用橙色菱形引导
  - 总结总览（布局 H-2）：紧凑列表，冠军用特殊标识，底部CTA引导行动

#### 更新的 Skill 章节
- `references/styles/minimal-warm.md`：新建极简暖色风格定义
- `references/canvases/solid-canvas.md`：新建纯色画布定义
- `references/layouts/layout-patterns.md`：新增布局 H、H-2、I，含 minimal-warm 风格示例
- `SKILL.md`：风格 Gallery 增加 minimal-warm，画布 Gallery 增加 solid-canvas，Step 2 布局表增加 H/H-2/I，版本更新至 v3.0
