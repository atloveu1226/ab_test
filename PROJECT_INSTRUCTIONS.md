# A/B Testing and Causal Inference Tutorial

## 项目目标

本项目基于 Hillstrom 邮件营销随机实验，构建一套完整的 A/B Testing 与 Causal Inference 分析流程。

项目需要回答以下业务问题：

> 邮件营销是否能够提升网站访问、购买转化和客户消费？男性商品邮件和女性商品邮件哪个效果更好？

同时，项目通过模拟选择偏差，说明在非随机 treatment assignment 下，如何使用因果推断方法估计处理效果。

## 数据集

数据包含 64,000 名客户，随机分配到三个实验组：

- `Mens E-Mail`
- `Womens E-Mail`
- `No E-Mail`

主要结果变量：

- `visit`：客户是否访问网站
- `conversion`：客户是否购买
- `spend`：观察期内的客户消费金额

实验前协变量包括：

- 最近购买时间 `recency`
- 历史消费 `history`
- 商品偏好 `mens`、`womens`
- 地区 `zip_code`
- 客户类型 `newbie`
- 历史购买渠道 `channel`

## Notebook 顺序与任务

### Notebook 01：Dataset Validation

目标：判断实验数据是否满足后续因果分析的基本条件。

需要完成：

- 读取并检查数据结构、缺失值、重复值和数据类型；
- 检查 `conversion = 1` 时是否必然 `visit = 1`；
- 检查 `spend > 0` 时是否必然 `conversion = 1`；
- 使用 DuckDB 完成数据注册和所有中间 SQL 查询；
- 使用 SRM 卡方检验验证三组样本比例；
- 比较各实验组的实验前协变量；
- 计算标准化均值差异（SMD）；
- 检查 `spend` 的零膨胀和右偏分布。

最终需要说明：实验分配、数据质量、逻辑一致性和协变量平衡是否满足要求。

### Notebook 02：Hypothesis Testing

目标：估计并检验各邮件活动的 treatment effect。

需要比较：

- `Mens E-Mail` vs `No E-Mail`；
- `Womens E-Mail` vs `No E-Mail`；
- `Mens E-Mail` vs `Womens E-Mail`。

需要完成：

- 对 `visit` 和 `conversion` 使用两比例 z 检验；
- 对 `spend` 使用 Welch's t-test；
- 计算 treatment effect 和 95% 置信区间；
- 对全部假设检验使用 Benjamini-Hochberg 校正；
- 对消费金额执行 Winsorization 敏感性分析；
- 将结果转换为每 10,000 名客户的业务增量。

最终需要给出具有统计意义和业务意义的 campaign recommendation。

### Notebook 03：Power Analysis

目标：评估当前实验设计能够检测多小的真实效果。

需要完成：

- 计算三个结果指标的 Minimum Detectable Effect（MDE）；
- 同时报告绝对效果和相对提升；
- 绘制 power curves；
- 绘制 sample-size curves；
- 比较原始消费金额和 Winsorized 消费金额的统计功效；
- 分析 effect size、variance、power 和 sample size 之间的关系。

最终需要说明：当前样本量可以检测哪些效果，以及检测更小效果需要多少样本。

### Notebook 04：Segmentation and Heterogeneous Treatment Effects

目标：判断 treatment effect 是否因客户群体而异。

分析范围限定为：

- `Mens E-Mail` vs `No E-Mail`。

需要完成：

- 根据 recency、历史消费、渠道、地区、商品偏好和客户类型构造客户分组；
- 计算各分组内的 treatment effect；
- 计算 95% 置信区间；
- 绘制 forest plots；
- 建立 treatment-by-segment interaction model；
- 使用 HC3 robust standard errors；
- 对 interaction tests 使用 Benjamini-Hochberg 校正。

最终需要区分描述性 subgroup variation 与具有统计证据的 treatment heterogeneity。

### Notebook 05：CUPED Variance Reduction

目标：判断实验前协变量是否能够提高 treatment effect 估计的精度。

需要完成：

- 对每个结果和候选协变量拟合回归并比较 R²；
- 实现单变量 CUPED；
- 实现多变量回归调整；
- 计算方差降低比例；
- 估计等效样本量节省；
- 比较调整前后的 treatment inference；
- 解释 CUPED 对二元结果变量的影响。

最终需要说明 CUPED 是否带来实际改善，以及实验前记录哪些数据可以提高 CUPED 的效果。

### Notebook 06：Causal Inference under Selection Bias

目标：演示 treatment assignment 非随机时，naive analysis 如何产生偏差，以及因果推断方法如何进行校正。

需要完成：

- 从随机实验中计算 experimental ATE 作为 ground truth；
- 构造 treatment assignment 存在选择偏差的 observational sample；
- 比较 treatment 与 control 的协变量分布；
- 计算 naive treatment effect；
- 估计 propensity score；
- 实现 nearest-neighbor propensity-score matching；
- 实现 inverse probability weighting（IPW）；
- 实现 doubly robust estimation；
- 检查协变量平衡和 propensity-score overlap；
- 将每种估计方法与 experimental ground truth 比较；
- 量化每种方法的 residual bias。

最终需要说明哪种方法最接近随机实验结果，以及剩余偏差可能来自哪些未观测变量或模型限制。

## 统计分析规范

- 在每个分析前明确 estimand；
- 明确 treatment comparison 的方向；
- 同时报告点估计和不确定性区间；
- 区分 statistical significance 与 practical significance；
- 在多重检验场景下进行适当校正；
- 不将“不显著”解释为“没有效果”；
- 使用因果推断方法时明确写出识别假设；
- 对 subgroup analysis 保持谨慎；
- 使用 matching 或 IPW 时检查 covariate balance、positivity 和极端权重；
- 不能把模拟实验结果直接当作真实 observational study 中的因果识别保证。

## Notebook 格式要求

每个 notebook 应包含：

1. Research question
2. Analytical objective
3. Statistical framework
4. Assumptions and limitations
5. TODO-based analysis cells
6. Required output
7. Student-written interpretation

练习版 notebook 的要求：

- 保留必要的 imports、数据读取和变量框架；
- 将关键 Python 分析代码替换为 TODO；
- 将所有 SQL 查询替换为 TODO；
- 删除所有历史输出、图片、日志和 execution counts；
- 不在 Summary 中直接提供标准答案；
- 学习者完成 TODO 后自行生成结果并撰写结论。

## 最终交付内容

完成后的项目应包括：

- 经过验证的实验数据；
- 三组之间的 treatment-effect estimates；
- 多重检验校正后的显著性结论；
- power 和 sample-size 分析；
- 分组 treatment-effect 分析；
- CUPED 方差缩减分析；
- 选择偏差下多种因果估计方法的比较；
- 基于统计证据和业务影响的最终 campaign recommendation。
