# A-Level 数学题库研究与 LeetMath 集成规划

> 上海学校生态 · 国际考试局 · 真题/专题题库 · 合规采集 · WeBWorK/PG · LeetMath 数据与产品路线  
> 研究基线：2026-11-18；**研究文档，不代表已完成抓取、授权、部署或校方合作。**

本仓库把围绕上海 A-Level 数学题库与 WeBWorK 的系列调研和会话结论整理为可维护的产品与工程文档。目标不是复制未经许可的历年试卷，而是构建**官方考试索引 + 许可明确的内容来源 + 原创参数化练习 + 可审计评分**的可扩展数学学习系统。

## 阅读顺序

| 文档 | 内容 |
| --- | --- |
| [00 研究结论与证据等级](docs/00-overview.md) | 目标、决策、事实/推断/待验证区分 |
| [01 考试局与课程结构](docs/01-exam-boards.md) | CAIE 9709/9231、Edexcel IAL、OxfordAQA |
| [02 上海学校与需求验证](docs/02-shanghai-schools.md) | 学校名单、访谈及商业试点路径 |
| [03 题库网站与竞品](docs/03-source-landscape.md) | 官方、专题、逐题、开放题库与采集价值 |
| [04 获取方式与版权矩阵](docs/04-rights-and-acquisition.md) | 爬虫前置条件、禁止项、版权门禁 |
| [05 WeBWorK 技术全景](docs/05-webwork-overview.md) | OPL、PG、MathObjects、WeBWorK2、Renderer |
| [06 PG 题目模型与判分](docs/06-pg-grading.md) | 参数化、seed、表达式等价、M/A/B 评分 |
| [07 部署与安全](docs/07-renderer-deployment.md) | Docker、API、隔离、故障恢复 |
| [08 LeetMath 数据模型](docs/08-data-model.md) | 考纲/题目/版权/实例/作答数据库设计 |
| [09 合规采集与导入](docs/09-ingestion-pipeline.md) | Source Registry、OPL 审计、ETL、去重 |
| [10 产品 PRD 与里程碑](docs/10-product-prd.md) | 学生端、教师端、双语、Manim/3D、阶段规划 |
| [11 验收、风险和决策日志](docs/11-validation-and-decisions.md) | 真实性能、版权、质量、未决假设 |
| [12 资料与核验索引](docs/12-references.md) | 入口、官方文档、条款、复查计划 |

## 核心决策

1. 第一阶段以 **Cambridge 9709** 作为主要考纲，之后扩展 **9231 和 Pearson Edexcel IAL**；各考试局及考纲版本彼此隔离。
2. **官方历年真题建立链接/元数据索引**，不将网上可下载等同于可转载；公开练习优先独立原创或明确可再许可的内容。
3. WeBWorK 最值得复用的是 **PG 参数化出题与 MathObjects 答案比较**，不是把 OPL 题库整体搬进商业产品。
4. LeetMath 保留学生、题单、Anki 式复习、教师、双语解释、Manim/Three.js 等业务系统；WeBWorK Renderer 只作为隔离的可选评分服务。
5. 所有题目必须可追溯：来源、许可证、考纲版本、内容哈希、随机种子、渲染器版本、评分证据。
6. **先验证，再扩张**：优先做 OPL 元数据审计、原创 PG 测试题、判分基准和学校访谈。

## 内容状态

- **已完成**：研究汇总、技术架构、数据库草案、试验方案、来源清单。
- **尚未完成**：外部网站批量爬取、第三方题目转载授权、WeBWorK 真机渲染基准、上海学校正式访谈、LeetMath 生产接入。
- 题量、竞品自报数据、学校使用的实际考试局均需再次核实，不作为已验证市场规模。
- 本仓库不托管 Cambridge/Pearson 未经许可的试题、评分标准或第三方付费题库内容。

## 来源原则

优先以考试局、学校官网、开源项目官方仓库和原始许可证为依据。二手目录/竞品页面只能证明页面宣称内容，不代表其拥有可转授权权利。完整引用见 [资料索引](docs/12-references.md)。

## 关联项目

- [LeetMath](https://github.com/1998x-stack/LeetMath)
- [manim_math](https://github.com/1998x-stack/manim_math)
- [WeBWorK Open Problem Library](https://github.com/openwebwork/webwork-open-problem-library)
- [WeBWorK PG](https://github.com/openwebwork/pg)
- [Standalone PG Renderer](https://github.com/openwebwork/renderer)

---

文档遵循：**来源可追溯、结论有等级、风险先行、代码可验证、版本可更新**。
