# Initial Plan

## 图像目标

绘制一张学术论文插图风格的 Reformer LSH Attention 机制图，分为“左侧流程图”和“右侧矩阵对比图”两大区块，服务于方法说明而非品牌展示。

## 结构概览

### 左侧区域：LSH Attention 五阶段流程

采用自上而下的五层排布，每一层保持 16 个 token 单元，体现同一序列在不同处理阶段中的组织方式变化。

1. `Sequence of queries + keys`
   - 16 个白色 token 方块
   - 对齐整齐，表示未处理原序列
2. `LSH bucketing`
   - 保持 16 个 token 数量不变
   - 用蓝、黄、红、灰四种低饱和颜色编码 bucket
3. `Sort by LSH bucket`
   - 将相同颜色 token 连续聚合
   - 表达按 bucket 排序后的序列
4. `Chunk sorted sequence to parallelize`
   - 按排序结果切成 4 个 chunk
   - chunk 与 chunk 之间留出明显空隙
5. `Attend within same bucket in own chunk and previous chunk`
   - 保持 chunk 化布局
   - 在 chunk 内及相邻前一 chunk 之间增加灰色弧线连接

### 右侧区域：四个注意力矩阵

采用 2×2 网格排布，四个矩阵大小一致，每个矩阵附带 `(a)` `(b)` `(c)` `(d)` 标注和说明标题。

1. `(a) Normal`
   - 稀疏散点式激活
2. `(b) Bucketed`
   - 形成同 bucket 的彩色局部方块
3. `(c) Q = K`
   - 沿对角线形成彩色三角趋势
4. `(d) Chunked`
   - 对角线上呈块状局部注意力区域

## 风格策略

- 背景：纯白
- 线条：细黑线
- 配色：低饱和蓝、黄、红、灰
- 字体：标题偏 Times New Roman 风格，标签可接受 Arial 风格
- 视觉语气：严谨、工程化、论文插图感

## XML 可编辑策略

- 所有 token 保持为独立矩形
- 所有标题和注释保持为文本节点
- 所有矩阵单元保持为独立方格
- 流程箭头与弧线保持为可编辑连线

## 已知未决点

- 无显著未决点
- 可在 refined plan 中补足更精确的尺寸、坐标、颜色和分组规则

