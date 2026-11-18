# 00 · 研究范围、核心结论与证据等级

**研究日期**：2026-11-18。**对象**：上海 A-Level 数学教育需求、国际考试局、题库获取、开源数学引擎及 LeetMath 集成。  
**文档性质**：对本轮会话及公开资料的整理；不是学校合同、法律意见或已执行工程的验收报告。

## 1. 目标与产品定位

LeetMath 已规划国内数学题库、题目/小问/标签/评分、题单、作答记录、Anki 式间隔复习、双语解释、Manim 视频与三维可视化。A-Level 应是共享数学底座上的**独立课程产品线**，不应把 9709/9231/IAL 原题混入上海高考数学。

教育路径建议：上海初中/中考数学 → 国际高中入学测试 → IGCSE/Pre-A → Cambridge AS → A2 → Further Mathematics。各路径覆盖和关联方式有待学校访谈确认。

## 2. 本轮可执行的六项决策

| 决策 | 原因 | 风险边界 |
| --- | --- | --- |
| 先 CAIE 9709，再 9231 和 Pearson IAL | 考纲与卷别清楚、便于 MVP | 不是上海学校市场份额结论 |
| 真题做官方链接索引、公开练习做原创题 | 版权限制显著 | 不因 PDF 公开下载而复制发布 |
| 研究 Examaced、Exam-Mate 等逐题产品 | 借鉴题号、标签和交互 | 题量属平台自报，不能相加 |
| OPL 用于元数据审计和技术原型 | 题目源码可分析、PG 判分成熟 | OPL 默认非商业许可 |
| 独立 PG Renderer 作为候选内部服务 | 避免重写数学比较器 | 运行不可信 Perl/PG 需要隔离 |
| 教师工作流与学生复习统一在 LeetMath | 可复用现有前后端能力 | 分步评分不能只依赖表达式等价 |

## 3. 证据等级

- **A：官方一手资料**：考试局考纲、条款、学校官网、开源许可证/README。可用作事实引用，但仍须记录访问日期和版本。
- **B：项目或平台自述**：题库宣传数量、功能介绍、考试中心目录；只表明其公开声称，不代表独立审计结论。
- **C：研究性映射**：OPL 标签 → 9709、产品机会、部署复杂度与阶段优先级，均为待验证设计。
- **D：未经验证的假设**：上海真实学生总量、付费转化、学校采购意愿、PG 兼容率、某校实际采用的考试局。

## 4. 关键事实

- Cambridge 9709 官方页面提供 2026–2027 及 2028–2030 考纲；正式 A-Level 存在 Pure+Mechanics+Statistics 和 Pure+Statistics 两条路线。
- Cambridge 9231 是 Further Mathematics，适合高水平学生；其试卷与 9709 分离管理。
- Pearson Edexcel International A Level Mathematics 是独立的模块化国际课程，不应与英国本土 GCE Edexcel 混同。
- OxfordAQA 旧数学资格 9660 将转入新版 9760：新方案 2027 首次教学，2028 首次 AS、2029 首次 A2 考试。
- Cambridge 官方明确不允许将历年考卷发布到其他网站；常规版权许可也不授权把历年题目作为电子题库发布。
- WeBWorK 开源软件与 OPL 内容许可彼此独立；OPL 默认 CC BY-NC-SA 3.0（个别文件可能不同）。

核查来源：[CAIE 9709](https://www.cambridgeinternational.org/programmes-and-qualifications/cambridge-international-as-and-a-level-mathematics-9709/) · [CAIE 9231](https://www.cambridgeinternational.org/programmes-and-qualifications/cambridge-international-as-and-a-level-mathematics-further-9231/) · [Pearson IAL](https://qualifications.pearson.com/en/qualifications/edexcel-international-advanced-levels/mathematics-2018.html) · [OxfordAQA 9760](https://www.oxfordaqa.com/qualification/international-as-and-a-level-mathematics-9760/) · [Cambridge 发布限制](https://help.cambridgeinternational.org/hc/en-gb/articles/203544371-Can-I-reproduce-Cambridge-past-examination-papers-on-the-school-s-website-my-website) · [OPL 许可证](https://wiki.openwebwork.org/wiki/Open_Problem_Library)。

## 5. 仍需通过研究解决的问题

1. 上海具体校区在 2026/27 实际采用 CAIE、Pearson、OxfordAQA 中哪一种？有没有 Further Maths？
2. 国际高中入学数学最主要的痛点是英语术语、思路转换、知识跨度还是题量？
3. 学生习惯先做整卷、按专题练还是错题复训？收费用户到底为哪些功能付费？
4. 原创题的分步评分准确度能否达到教师可用标准？哪些场景必须人工复核？
5. OPL 的微积分/概率类题有多少真正符合 9709/9231 的考纲、语言与难度？
6. 对外许可、学校内部授权与商业软件托管边界应该如何分别实施？

## 6. 本仓库的边界

本文档不宣称已经爬取任何第三方付费题库、导入真实 Cambridge 试题、完成 WeBWorK 真实部署或取得学校合作；相关内容属于**可执行设计**而非实际运行日志。

下一步请按 [课程结构](01-exam-boards.md) → [来源与权限](04-rights-and-acquisition.md) → [WeBWorK 集成](05-webwork-overview.md) → [PRD](10-product-prd.md) 阅读。
