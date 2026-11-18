# 01 · 考试局、课程与试卷结构

## 1. 为什么要分考试局

A-Level 不是统一的单套数学卷。Cambridge International AS & A Level、Pearson Edexcel International A Level（IAL）、OxfordAQA International 以及英国本土 GCE A Level 均有自身资格代码、考纲、考卷和成绩组合规则。LeetMath 必须保存 **board + qualification_code + syllabus_version + component + examination_session + paper_variant**。

## 2. Cambridge International Mathematics 9709

官方课程页：[9709 Mathematics](https://www.cambridgeinternational.org/programmes-and-qualifications/cambridge-international-as-and-a-level-mathematics-9709/)。以 **2026–2027** 考纲为第一版结构基线，2028–2030 另建版本。

| 卷 | 名称 | 主要领域 | 备注 |
| --- | --- | --- | --- |
| Paper 1 | Pure Mathematics 1 | 二次函数、函数、解析几何、弧度、三角、级数、求导、积分 | AS/A-Level 共同基础 |
| Paper 2 | Pure Mathematics 2 | 代数、对数指数、三角、微积分、数值解 | AS 的特定路线，不计入完整 A-Level |
| Paper 3 | Pure Mathematics 3 | 进阶代数、对数指数、三角、微积分、数值法、向量、微分方程、复数 | A2 |
| Paper 4 | Mechanics | 力与平衡、运动、动量、牛顿定律、功与能 | 应用数学 |
| Paper 5 | Probability & Statistics 1 | 数据表达、排列组合、概率、离散随机变量、正态分布 | 应用数学 |
| Paper 6 | Probability & Statistics 2 | 泊松、线性组合、连续变量、抽样估计、假设检验 | 应用数学 |

**典型合法组合（2026–2027）**：

- AS：P1+P2，或 P1+P4，或 P1+P5。
- 完整 A-Level：P1+P3+P4+P5，或者 P1+P3+P5+P6。
- **P1+P2 的 AS 不能沿此路径累加成完整 A-Level**。P4 与 P6 不能随意拼接。
- 记录试卷年、考试季、variant/time zone、替换卷/考试安全特殊事件，不能只存 paper_number。

本节卷组合在产品中必须用规则引擎实现，不能靠自由文本标签。详情以下载的当年官方 syllabus 的 assessment/combination 章节为最终依据。

## 3. Cambridge Further Mathematics 9231

[9231 官方课程](https://www.cambridgeinternational.org/programmes-and-qualifications/cambridge-international-as-and-a-level-mathematics-further-9231/) 与 [历年试卷示例](https://www.cambridgeinternational.org/programmes-and-qualifications/cambridge-international-as-and-a-level-mathematics-further-9231/past-papers/)。

| Paper | 名称 | 示意领域 |
| --- | --- | --- |
| 1 | Further Pure Mathematics 1 | 高次方程根、有理函数、级数、矩阵、极坐标、向量 |
| 2 | Further Pure Mathematics 2 | 双曲函数、复数、微分方程、更多矩阵与微积分 |
| 3 | Further Mechanics | 抛体、刚体平衡等 |
| 4 | Further Probability & Statistics | 更高阶概率统计 |

完整 A-Level 要求四卷；AS 路线则按当年考纲组合。9709 与 9231 的题库、成绩与知识点必须保持独立实体，通过共享概念做映射。

## 4. Pearson Edexcel IAL

[官方网站](https://qualifications.pearson.com/en/qualifications/edexcel-international-advanced-levels/mathematics-2018.html) · [数学规范 PDF](https://qualifications.pearson.com/content/dam/pdf/International%20Advanced%20Level/Mathematics/2018/Specification-and-Sample-Assessment/international-a-level-maths-spec.pdf)。

课程包含 P1–P4（Pure）、FP1–FP3（Further Pure）、M1–M3（Mechanics）、S1–S3（Statistics）、D1（Decision）。有 Mathematics、Further Mathematics、Pure Mathematics 等资格，需要遵守各自的有效单元及复用规则。

典型的 Mathematics 组合会包含 P1、P2、P3、P4，并从规范允许的应用单元中选择两个。**不能简单认定任意 6 个 unit 构成一个资格**；数据库必须另建 qualification_award_rules 和 version。

英国本土 Edexcel GCE（如 9MA0）与 Pearson IAL **不同**；不可共享试卷编号或以名称相似作自动合并。

## 5. OxfordAQA International

- 旧版 [9660](https://www.oxfordaqa.com/qualification/international-as-and-a-level-mathematics-9660/) 正在被新版取代。
- 新版 [9760](https://www.oxfordaqa.com/qualification/international-as-and-a-level-mathematics-9760/) **2027 首次教学，2028 首次 AS 考试，2029 首次 A2 考试**，采用 4 个必修 Pure + 2 个允许组合的应用单元。
- 对学生历史数据按首次教学年与实际考试年双字段处理，不要直接覆盖 9660。

## 6. 数据约束建议

- <code>syllabus_versions</code>：考试局、资格、官方有效考试年、来源 URL、发布版本。
- <code>exam_components</code>：P1/P2/M1 等唯一性只在资格和考纲版本内成立。
- <code>exam_papers</code>：考试季/year/variant/status/source_url。
- <code>award_pathways</code> 与 <code>award_pathway_components</code>：可验证课程组合。
- <code>question_topic_links</code>：多对多关联 + 版本 + 人工确认。
- 历史题目不能仅因知识点名称一致就自动列入新版考纲，需要显式标注 out-of-syllabus 或 review_needed。

## 7. 课程与题型优先顺序

P0：9709 P1、P3；P1 原创训练和基础分步评分。  
P1：9709 P4、P5、P6 与 9231 基础；Pearson IAL P1–P4。  
P2：完整 Pearson Further / OxfordAQA；IGCSE、上海国际高中入学数学。

这些是**实施优先级建议**，不是考试局官方排名。
