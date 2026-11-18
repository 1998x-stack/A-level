# 03 · A-Level 数学题库网站、竞品与获取价值

> “网页可读取”与“可批量复制、训练模型、再发布”完全不同。本页仅整理可研究的网站与产品能力，采集权限见 [版权与获取](04-rights-and-acquisition.md)。

## 1. 官方考试资料

| 来源 | 主要资源 | 建议动作 |
| --- | --- | --- |
| [Cambridge 9709](https://www.cambridgeinternational.org/programmes-and-qualifications/cambridge-international-as-and-a-level-mathematics-9709/past-papers/) | 部分公开 Paper、Mark Scheme、Examiner Report、样卷 | 官方 URL/年份/卷别索引，不搬运试题 |
| [Cambridge 9231](https://www.cambridgeinternational.org/programmes-and-qualifications/cambridge-international-as-and-a-level-mathematics-further-9231/past-papers/) | Further Maths 历年资料 | 同上 |
| [Pearson IAL](https://qualifications.pearson.com/en/support/support-topics/exams/past-papers.html) | IAL 模块试卷，部分锁定 | 只用公开授权检索和官方链接 |
| [OxfordAQA](https://www.oxfordaqa.com/subjects/mathematics/) | 9660/9760 考纲、样卷及教师支持 | 考纲版本元数据 |

官方页面有时只公开少数试卷；Cambridge School Support Hub 和 Pearson 教师访问权限不意味着公众可获取或转载。

## 2. 竞品与专题题库

| 站点 | 主要能力 / 产品差异 | 优先研究内容 | 风险状态 |
| --- | --- | --- | --- |
| [Exam-Mate](https://www.exam-mate.com/) | 考试局/专题/年份筛选 | 标签结构、专题汇编 | 先申请授权 |
| [Physics & Maths Tutor](https://www.physicsandmathstutor.com/maths-revision/) | 试卷、答案、专题 PDF/学习资料 | 目录设计 | 使用条款待核验 |
| [Save My Exams](https://www.savemyexams.com/) | 考纲化笔记、专题习题、Flashcards | 学习路径、付费转化设计 | 条款禁止爬虫 |
| [Dr Frost Learning](https://www.drfrost.org/) | 在线习题、课堂作业、教师分析 | 互动判分和教师端 | 条款应逐次核验；不可默认抓取 |
| [Examaced](https://www.examaced.com/) | CAIE 按卷/专题逐题练习 | 题目 ID、成绩记录、学习流 | 不实施未经许可的批量获取 |
| [PastPaper.cc](https://pastpaper.cc/) | 逐题目录及专题分类 | 来源关联、metadata | 需核实权属和站点条款 |
| [Momentum Studies](https://www.momentumstudies.com/topical/cambridge/a-level/mathematics-9709) | 9709 按 topic/year 的目录 | 考纲覆盖、专题统计 | 账户内容不自动提取 |
| [Maths Genie](https://www.mathsgenie.co.uk/) | 英国 A-Level 练习 | 英国本土题型与补充学习 | 条款禁止自动提取 |
| [Margin](https://www.studymargin.com/papers) | CAIE 官方资料索引 | 试卷编号模型 | 优先回溯官方源 |

部分页面自述的题量曾在会话中提及：Examaced 9709 约 2,416、9231 约 1,178；PastPaper.cc 9709 约 5,184、9231 约 2,105；Momentum 9709 约 4,992。**这些数字不具备统一口径，未独立核验，不能相加、不用于市场规模估算。**

## 3. 开放源码数学题目

| 来源 | 内容形态 | 主要优点 | 许可限制 |
| --- | --- | --- | --- |
| [WeBWorK Open Problem Library](https://github.com/openwebwork/webwork-open-problem-library) | .pg 源码、随机参数、答案比较器、元数据 | 适合做题目实例和判分实验 | 默认 CC BY-NC-SA 3.0，逐文件核验例外 |
| [OpenStax Precalculus 2e](https://openstax.org/details/books/precalculus-2e) | 教材与练习 | 概念桥接、独立命题参考 | 需核对具体资源版本、CC 许可和商业限制 |
| LeetMath 原创题 | JSON/LaTeX/PG | 可完整控制审核、题解、版本 | 确保原创、不侵权 |

## 4. 竞品启示（产品假设，不是竞品缺陷定论）

- **从 PDF 到逐小问**：用户需要结构化练习、错题重做、过程得分，而非只下载试卷。
- **知识点 + 考试局 + 版本**：相似题目应映射 CAIE、Edexcel 和上海数学的各自标准，而非共用一个模糊标签。
- **学生体验**：屏幕顶部保留进度/分数；公式和可视化应无障碍；推荐 Anki 式间隔复习。
- **教师体验**：快速组卷、批改、分析错误类型、复核 AI/PG 判分。
- **差异化**：双语讲解 + Manim + Three.js 能增加复杂数学题的可解释性。

## 5. 竞品调研表模板

每次调研记录：<code>site</code>、<code>checked_at</code>、<code>board</code>、<code>qualification</code>、<code>content_format</code>、<code>access_model</code>、<code>automation_policy</code>、<code>redistribution_policy</code>、<code>verified_source_url</code>、<code>confidence</code>、<code>analyst_notes</code>。

定期复查：商业站点条款每季度，考试局每个考纲周期，学校采用课程每学年。
