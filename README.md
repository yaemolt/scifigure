# SciFigure

`scifigure` 是一个面向 Codex/OMX 的科学图示工作流技能，用来把文字需求、参考图风格或可复用素材，整理成一套可审阅、可迭代、可在 `draw.io`/`diagrams.net` 中继续编辑的科学插图产物。

它不是“一步生成最终图片”的黑盒提示词，而是一个分阶段、多角色协作的流程，重点放在：

- 先规划，再出图
- 中间产物可审阅
- 最终结果优先保证 `draw.io XML` 可编辑
- 适合论文示意图、机制图、流程图、图形摘要等场景

## 适用场景

这个技能适合以下输入方式：

- 只有文字描述
- 有一张或多张参考风格图
- 有可复用的 `PNG` / `SVG` 资产
- 需要分阶段确认版式、视觉风格和文案
- 需要最终交付 `draw.io` 可编辑 XML，而不是单纯截图或位图

## 仓库结构

```text
.
├── SKILL.md
├── README.md
├── agents/
├── references/
├── test/
└── .omx/
```

各目录作用：

- `SKILL.md`：技能主入口说明
- `agents/`：各角色的职责定义
- `references/`：工作流、评分标准、XML 规范、产物规范
- `test/`：一个示例运行产物集
- `.omx/`：OMX 运行时状态与辅助文件

## 如何使用

在 Codex/OMX 中调用这个技能后，建议按下面的顺序执行。

### 1. 判断输入类型

先识别用户提供的是哪一种输入：

- 纯文本需求
- 参考风格图片
- 可复用 `PNG/SVG` 素材
- 当前运行目录中已存在标准产物 `output/11-final.drawio.xml`

对应策略：

- 纯文本：直接进入规划阶段
- 有参考图：先做风格分析，或与规划并行进行
- 有素材：在执行前补做资产分析
- 已有 `output/11-final.drawio.xml`：先进入修改模式，先分析已有 XML 的问题，再进入规划

### 2. 创建一次运行目录

每次任务使用一个独立目录，推荐格式：

```text
runs/<timestamp>-<topic>/
├── work/
└── output/
```

例如：

```text
runs/2026-04-20-signaling-pathway/
```

### 3. 保存原始需求

把用户原始输入写入：

```text
work/00-user-input.md
```

建议保留原始语言，不要过早重写需求。

### 4. 先过规划门

进入执行前，必须先完成规划阶段：

如果当前运行目录已经有标准产物 `output/11-final.drawio.xml`，则先增加一个修改分析步骤：

1. `modifier` 读取现有 `output/11-final.drawio.xml`
2. `modifier` 结合用户新的修改需求，输出 `work/07-xml-modification-analysis.md`
3. `planner` 再基于原始需求、已有分析结果和修改目标生成计划

这个步骤的作用是先判断：

- 现有 XML 哪些地方可以保留
- 哪些结构需要重建
- 哪些问题属于布局、视觉、文案或可编辑性问题
- 这次修改的重点应该放在哪里

然后再进入常规规划门：

1. `planner` 生成 `work/02-initial-plan.md`
2. `critic` 按 `references/planning-rubric.md` 打分
3. 如果任一维度 `<= 80`，由 `interviewer` 一轮只问一个问题进行澄清
4. `planner` 生成 `work/05-refined-plan.md`
5. 用户明确回复 `通过` 或提出修改意见

只有下面两个条件都满足，才能进入执行阶段：

- 规划评分全部高于 `80`
- 用户已批准 refined plan

### 5. 执行分工

执行阶段按角色拆分：

- `architect`：输出 `work/08-layout-spec.md`
- `drawer`：输出 `work/09-visual-spec.md`
- `writer`：输出 `work/10-copy-spec.md`
- `xml-drawer`：输出 `output/11-final.drawio.xml`
- `reviewer`：按 `references/review-rubric.md` 给出最终审阅报告

### 6. 审阅与迭代

如果最终审阅未通过，按问题类型回流：

- 版式问题：回到 `architect`
- 视觉/颜色问题：回到 `drawer`
- 文案问题：回到 `writer`
- 结构或可编辑性问题：回到 `xml-drawer`

目标是所有关键维度评分都高于 `80`。

## 标准产物

一次完整运行通常会生成以下文件：

```text
runs/<timestamp>-<topic>/
├── work/
│   ├── 00-user-input.md
│   ├── 01-intake-summary.md
│   ├── 02-initial-plan.md
│   ├── 03-critic-score-plan.md
│   ├── 04-qa-log.md
│   ├── 05-refined-plan.md
│   ├── 06-style-analysis.md
│   ├── 07-asset-analysis.md
│   ├── 07-xml-modification-analysis.md
│   ├── 08-layout-spec.md
│   ├── 09-visual-spec.md
│   └── 10-copy-spec.md
└── output/
    ├── 11-final.drawio.xml
    └── 12-review-report.md
```

说明：

- `work/06-style-analysis.md`、`work/07-asset-analysis.md` 和 `work/07-xml-modification-analysis.md` 都是可选文件
- `work/` 保存中间文档，`output/` 保存最终交付物
- 文件名建议保持稳定，方便自动化检查和人工审阅
- 全部文档应尽量使用用户输入语言

## 一个最小使用示例

假设用户需求是：

> 画一个细胞信号通路机制图，包含细胞膜、受体、胞内级联和核内转录激活，风格简洁、适合论文主图。

可以按下面方式组织一次运行：

```text
runs/2026-04-20-cell-signaling/
├── work/
│   ├── 00-user-input.md
│   ├── 02-initial-plan.md
│   ├── 03-critic-score-plan.md
│   ├── 05-refined-plan.md
│   ├── 08-layout-spec.md
│   ├── 09-visual-spec.md
│   └── 10-copy-spec.md
└── output/
    ├── 11-final.drawio.xml
    └── 12-review-report.md
```

如果用户又补充了一张参考图，则增加：

```text
work/06-style-analysis.md
```

如果用户提供了现成的图标素材，则增加：

```text
work/07-asset-analysis.md
```

## 关键规则

- 始终优先保证 `draw.io` 可编辑性，而不是只追求视觉拟真
- 不要跳过规划阶段
- 每轮澄清最多问一个问题
- 澄清轮次最多 `8` 轮
- 角色边界要清晰，避免越权代做
- 优先保留明确的中间文档，而不是把推理藏在一次性输出里

## 参考文件

如果你要扩展或维护这个技能，优先阅读：

- `SKILL.md`
- `references/workflow.md`
- `references/run-artifacts.md`
- `references/drawio-xml-guidelines.md`
- `references/planning-rubric.md`
- `references/review-rubric.md`

## 当前仓库状态

仓库内已经包含：

- 一套完整的角色定义：`agents/`
- 一组参考规范：`references/`
- 一个样例产物集：`test/`
- `agents/openai.yaml`，可用于相关代理配置场景

如果后续要对外发布，建议再补充：

- `LICENSE`
- 更完整的真实案例截图
- 自动验证脚本与发布说明
