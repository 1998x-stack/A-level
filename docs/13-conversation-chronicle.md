# 13 · 本轮会话研究内容与决策演变

这不是逐字转录，而是把本轮围绕上海 A-Level 数学题库的三段研究**全部主要问题、建议、限制与下一步**转换成可维护的知识文档。所有未验证项均明确标记，避免误认成实际已部署功能。

## 一、上海学校与 A-Level 数学体系

用户首先提出：“A-level 相关题库，上海学校，正常深度调研”。

研究结论包含：Cambridge 9709/9231、Pearson Edexcel IAL、OxfordAQA 的差异；9709 六卷及 AS/A-Level 有效组合；9231 Further Mathematics；上海光华剑桥、领科、阿德科特、康德、WLSA 候选学校；中考→国际高中入学→IGCSE→AS→A2 的课程衔接；中文数学术语、分步评分、教师作业和学校采购问题。

由此提出 LeetMath A-Level 作为独立课程线，和上海高考数学共享数学基础能力，但不混合考试局考纲、试卷和评分规则。

相关内容：[课程](01-exam-boards.md) · [上海学校](02-shanghai-schools.md) · [产品 PRD](10-product-prd.md)。

## 二、现有题库和爬虫

第二个问题是：“有没有现有的数学题库网站，分别如何获取甚至爬虫？”

讨论官网与平台：Cambridge、Pearson、Exam-Mate、Physics & Maths Tutor、Save My Exams、Dr Frost、Examaced、PastPaper.cc、Momentum、Maths Genie、Margin、WeBWorK OPL。

输出的方向包括：官方真题链接索引、专题分类研究、第三方授权合作；区分网页可访问与再发布权利；遵守爬虫条款、robots、登录/付费门禁；对于确已授权的 PDF 采用 PyMuPDF、版面解析、多模态视觉、数学公式/图像重建、教师审核。

过去会话中还形成一个本地采集原型 ZIP（catalog_crawler.py、index_webwork_pg.py 与 README），用于授权网站的链接索引与本地 PG 元数据扫描。**本次仓库文档不宣称已将 ZIP 的可执行文件同步进仓库，也不宣称对外站点跑过全量抓取。**

相关内容：[来源清单](03-source-landscape.md) · [法律门禁](04-rights-and-acquisition.md) · [ETL](09-ingestion-pipeline.md)。

## 三、WeBWorK 深入分析

第三个问题是：“WeBWorK 详细展开”。

研究拆分为 OPL（题目内容）、PG/PGML/MathObjects（参数化出题、渲染和数学判分）、WeBWorK2（完整课程系统）、Standalone Renderer（HTTP API）、WeBWorK3（后续参考）。

深入讨论了 OpenProblemLibrary / Contrib / Pending 目录、PG 头部 DBsubject 等元数据、seed 重放、表达式比较与定义域陷阱、MultiAnswer/Scaffold、多步骤部分得分、Cambridge M/A/B 独立评分层、Docker Renderer 的 POST /render-api、Perl 源码沙箱风险，以及 OPL 默认 CC BY-NC-SA 3.0 非商业许可与程序开源许可之间的区别。

核心建议：先做 OPL 元数据及许可证审计，用原创 PG 题真实测试 Renderer 和数学判分，再决定是否接入 LeetMath 生产环境；不要把 OPL 全量题库当作商业可用的 A-Level 真题仓库。

相关内容：[WeBWorK 架构](05-webwork-overview.md) · [PG/判分](06-pg-grading.md) · [部署安全](07-renderer-deployment.md) · [数据模型](08-data-model.md) · [验收](11-validation-and-decisions.md)。

## 四、本次仓库沉淀

用户要求把所有上述调查和会话内容沉淀到 [1998x-stack/A-level](https://github.com/1998x-stack/A-level)。

实施方式是 README + docs/00–13 共 14 篇文档，围绕证据等级、考试局、上海学校、竞品、版权、WeBWorK、PG、Renderer、数据库、ETL、PRD、测试与出处系统化组织。

这些文档是研究与方案资产，**不等于**已经在 LeetMath 部署、批量采集第三方题目、获得学校合作或者完成真实 PG 渲染测试。相关下一阶段验收清单见 [11](11-validation-and-decisions.md)。

## 五、建议下一轮实际开发顺序

1. 读取现有 LeetMath schema 并做差异化迁移计划。
2. 固定 OPL git commit，运行本地 .pg 元数据审计。
3. 启动隔离 Renderer，用原创题执行多 seed 和评分回归。
4. 建立 CAIE 9709 P1/P3 原创题和逐小问评分 MVP。
5. 走访上海至少三所学校，用真实需求调整优先级。
