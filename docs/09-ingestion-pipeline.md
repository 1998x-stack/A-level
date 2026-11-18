# 09 · 合规采集、OPL 本地审计与数学题 ETL

## 1. 统一流水线

~~~text
Source Registry / 授权记录
          ↓
Policy Check (不符合规则则拒绝)
          ↓
Connectors: 官方索引 | 获许可 PDF | 本地 .pg | 原创内容
          ↓
Raw metadata + provenance
          ↓
Parse → Normalize → Deduplicate → Syllabus Mapping
          ↓
Math QA + Teacher QA + Rights QA
          ↓
internal_only / link_only / publishable
          ↓
LeetMath DB / Search / Practice Engine
~~~

**优先做统一连接器与强发布门禁，而不是为每家竞品写未经授权的爬虫。** 采集技术不等于许可，详细规范见 [04](04-rights-and-acquisition.md)。

## 2. Source Registry 示例

~~~yaml
source_id: github-openwebwork-opl
source_kind: git_repository
origin_url: https://github.com/openwebwork/webwork-open-problem-library
permission_checked_at: 2026-11-18
acquisition: git_clone
rights_default: CC-BY-NC-SA-3.0
rights_exceptions: per_file_review
allowed_uses: [local_metadata_audit]
publication_approved: false
~~~

只是研究登记示例，并不构成 OPL 全库商业发布授权。网站类来源另外保存 terms_version、robots_checked_at、permission_document_id、rate_limit、allowed_paths、retention_policy。

## 3. 官方试卷索引

只存 board、qualification、syllabus、session、year、component、variant、revision、document_kind、canonical_url、checked_at。除非取得许可，**不自动下载和托管题目 PDF/评分标准**，也不处理受限登录资源。题目正文另走内容授权流程。

## 4. OPL 的优先审计方案

~~~bash
git clone --depth 1 https://github.com/openwebwork/webwork-open-problem-library.git
git -C webwork-open-problem-library rev-parse HEAD

find webwork-open-problem-library/OpenProblemLibrary \
  -type f -name '*.pg' | wc -l
~~~

建议把 .pg 作为**静态文本索引**，先不执行：

1. 识别 DBsubject、DBchapter、DBsection、Level、KEYWORDS、Author、Institution、Language。
2. 输出 source_path、file_sha256、git_commit、metadata_parse_status。
3. 扫描 MathObjects.pl、PGML.pl、Scaffold/MultiAnswer、图片/宏/JS 等依赖。
4. 统计 Subject/Chapter/Section、难度与宏使用分布。
5. 用关键词/模型生成 CAIE 9709/9231 候选映射，记录 confidence、syllabus_version、review_status。
6. 只有经过人工验证才能计入“可用 A-Level 原创改写候选”；原题版权另行审核。

## 5. 合法 PDF 解析

对有明确处理权的 PDF：PyMuPDF/pdfplumber 提取版面 → 视觉理解数学图形与公式 → 拆成 question_parts → LaTeX/Markdown/参数化图形 → 评分验证 → 教师审阅。不要依赖 OCR 单一路径；复杂 3D 需保留视觉证据与多视角图。

## 6. 去重和版本

三层检测：原文件 SHA-256、题目规范化文本/公式哈希、语义相似候选。语义近似**不能**自动判定为同一题或推断权利关系。模板随机生成的多个 instance 不应当算作不同版权作品或不同知识点。

## 7. 状态机与审计输出

~~~text
discovered → policy_check
  ├─ denied/needs_license → blocked
  └─ allowed → collected → parsed → normalized → reviewed
                                              ├─ rejected
                                              ├─ internal_only
                                              ├─ link_only
                                              └─ publishable
~~~

建议输出 catalog.jsonl、rights_report.csv、topic_inventory.csv、mapping_candidates.csv、parse_errors.jsonl、render_seed_results.jsonl、run_manifest.json。每次报告保存数据范围、构建 commit、执行时间、许可证核验结果，所有输入与输出需要幂等。

## 8. 停止条件

401/403、验证码、疑似反爬、权限到期、授权撤销、第三方内容未明确权属、学校私有数据、下载超出协议范围时立即停止相关连接器。任何来源的 raw_data 在权利不明确时不得发布到公共题库。
