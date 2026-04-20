# 用户输入

绘制一张学术论文风格的 Transformer 优化架构图，主题为 Reformer 中的 LSH Attention（局部敏感哈希注意力）流程示意图。整体采用黑白底色 + 彩色模块点缀，排版类似顶会论文插图，简洁、严谨、工程感强。

画面横向展开，分为左右两个区域：

【左侧：流程结构图】
从上到下展示五层处理流程，每层由一排长方形 token 方块组成：

1. 第一行标题：Sequence of queries + keys
    一排白色小方块整齐排列，共16~20个，表示原始输入序列。
2. 第二行标题：LSH bucketing
    同样数量方块，但被染成不同颜色（蓝、黄、红、灰），表示通过哈希后被分配到不同桶（bucket）。
3. 第三行标题：Sort by LSH bucket
    方块重新排列，相同颜色连续聚集成段，表示按 bucket 排序后的序列。
4. 第四行标题：Chunk sorted sequence to parallelize
    排列后的 token 被切分成若干 chunk，每组之间留空隙，表示并行计算块。
5. 第五行标题：Attend within same bucket in own chunk and previous chunk
    在 chunk 内部及相邻前一 chunk 之间画灰色弧线连接，表示局部注意力计算路径。

整体要求箭头清晰、流程递进明显，体现：
输入序列 → 哈希分桶 → 排序聚类 → 分块并行 → 局部注意力

【右侧：四个注意力矩阵对比图（2×2 排列）】

每个矩阵是小型方格热图，带坐标标签 q1q6、k1k6：

(a) Normal
稀疏散点分布，表示原始全连接注意力。

(b) Bucketed
相同 bucket 的 token 形成彩色小块（蓝块、红块、黄块），表示哈希后局部聚类。

(c) Q = K
沿主对角线形成彩色三角区域，体现 query-key 对齐。

(d) Chunked
对角线上出现分块矩形区域，每块之间有边框，表示 chunk 局部注意力。

【风格要求】

- 顶会论文插图风格（ICLR / NeurIPS / ICML）
- 干净白底，细黑线，低饱和彩色点缀
- 字体类似 Times New Roman / Arial 学术字体
- 线条清晰，模块工整
- 信息密度高但整洁
- 像真实论文中的 Figure 2

