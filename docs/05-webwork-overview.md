# 05 · WeBWorK 技术全景、OPL 结构与 LeetMath 定位

## 1. 为什么研究 WeBWorK

[WeBWorK](https://openwebwork.org/) 是开源的数学/科学在线作业平台。公开安装与项目资料介绍 OPL 规模已达数万题（此前研究中提及 37,000+）；但这不是“37,000 道 CAIE A-Level 原题”，大多数内容对应美国数学课程/大学低年级课程。**题目数量只是来源库存，不是 LeetMath 可用/可商业发布数量。**

项目分层：

| 项目 | GitHub | 用途 | LeetMath 集成策略 |
| --- | --- | --- | --- |
| Open Problem Library（OPL） | [webwork-open-problem-library](https://github.com/openwebwork/webwork-open-problem-library) | .pg 题目、分类、资源文件 | 扫元数据、做审计；发布需许可 |
| PG / MathObjects / PGML | [pg](https://github.com/openwebwork/pg) | 随机参数、题目渲染、数学答案比较 | 重点技术验证 |
| WeBWorK2 | [webwork2](https://github.com/openwebwork/webwork2) | 教师、课程、作业、成绩/LMS | LeetMath 已有这些业务，原则上不整体引入 |
| Standalone PG Renderer | [renderer](https://github.com/openwebwork/renderer) | HTTP 渲染/判分服务 | 隔离部署为可选 adapter |
| WeBWorK3 | [webwork3](https://github.com/openwebwork/webwork3) | 下一代服务端与前端探索 | 跟踪，不作为当前依赖前提 |

## 2. OPL 的三个目录

官方 [README](https://github.com/openwebwork/webwork-open-problem-library) 区分：

- <code>OpenProblemLibrary/</code>：经过分类、可通过 Library Browser 的 Subject → Chapter → Section 检索的主体。
- <code>Contrib/</code>：作者提供的题目，可能未被官方编辑纳入正式目录。
- <code>Pending/</code>：编辑流程暂存，不应当作稳定正式题库。

正式数据分析先只统计主体目录，再另行审计 Contrib、Pending。

## 3. PG 文件不是静态文档

单个 PG 文件可以包含：
- 题目元数据（DBsubject、DBchapter、DBsection、Level、KEYWORDS、Author、Institution 等）；
- 可执行 Perl / PG 宏；
- 随机参数、显示文字和 LaTeX、答案表单；
- 标准答案比较器、反馈与解答；
- 外部资源（图片、宏、JavaScript 交互）。

PG 的对象不是“固定题目”，而是 **题目模板 → 指定 seed → 题目实例 → 答案验证**。同一题模板通常可以生成多个参数实例，应保存源码哈希、宏/渲染器版本、seed 才能准确回放。

## 4. 元数据示例（示意，不代表原题）

~~~perl
## DBsubject(Calculus - single variable)
## DBchapter(Differentiation)
## DBsection(Polynomial derivatives)
## Level(2)
## KEYWORDS('derivative', 'polynomial')
## Author(Example Contributor)
~~~

对 LeetMath：source_topic 保留原始标签；考试局知识点需要独立人工映射并按考纲版本追踪，而不是照抄原有分类。

## 5. 为什么不整体迁入 WeBWorK2

现有 LeetMath 的重点是：
- Shanghai Gaokao + A-Level + IGCSE 的多课程数学知识图谱；
- LeetCode 式题目/小问、进度与分数、题单；
- Anki 式复习调度与知识点掌握度；
- 双语数学术语、Manim 教学动画、Three.js 图形；
- 教师作业、分步评分、审核证据。

WeBWorK2 教学功能虽成熟，但强行替换会形成第二套用户/班级/权限/作业系统。因此建议 **保留 LeetMath 作为主系统，只将 PG 引擎能力通过 adapter 引入**。

## 6. 许可证边界

- [WeBWorK2 LICENSE](https://github.com/openwebwork/webwork2/blob/main/LICENSE)：GPL 2.0-or-later / Artistic 1.0 选择性许可。
- [PG LICENSE](https://github.com/openwebwork/pg/blob/main/LICENSE)：GPL 2.0-or-later / Artistic 1.0。
- [Standalone Renderer LICENSE](https://github.com/openwebwork/renderer/blob/main/LICENSE)：应核验具体部署的 commit/许可；此前调研记录为 GPL-3.0。
- [OPL 内容许可](https://github.com/openwebwork/webwork-open-problem-library/blob/main/OPL_LICENSE)：默认 CC BY-NC-SA 3.0，个别作品存在例外。

**软件许可证 ≠ 题目许可证。** 独立服务部署也不自动豁免 OPL 题目非商业限制。生产部署前请对固定的 Git commit 逐一复核许可及其义务。

## 7. 审计最值得统计的指标

总 .pg 文件数、Subject/Chapter/Section 覆盖、Level 分布、MathObjects 使用比例、PGML 使用比例、多空题/Scaffold 比例、资源依赖、随机种子稳定性、可用许可证分布、数学公式渲染失败率、对 9709/9231 映射成功率、人工审核拒绝原因。

没有这些结果，不能推断 OPL 对 A-Level 的实际覆盖率。
