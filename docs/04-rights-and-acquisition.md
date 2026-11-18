# 04 · 获取策略、站点爬虫与版权/许可证门禁

**原则**：技术可抓取 ≠ 法律许可 ≠ 可以公开发布 ≠ 可以用于模型训练。这四件事分别核查。

## 1. 四层内容来源策略

| 层级 | 来源 | 允许的默认操作 | 可否公开展示题干 |
| --- | --- | --- | --- |
| A 官方索引 | Cambridge、Pearson、OxfordAQA 官网 | 手动建立官方链接与考试元数据；自动化需再核实访问条款 | 默认否，仅显示链接和我们自己的摘要 |
| B 原创题 | LeetMath 独立创作的数学题 | 保存、审核、参数化、公开训练 | 在核实原创后可以 |
| C 开源/许可题 | OPL 或学校许可内容 | 依每文件/每协议范围处理 | 按许可证及用途决定 |
| D 第三方竞品 | Exam-Mate、Examaced、Save My Exams 等 | 竞品功能研究、授权洽谈 | 默认否 |

**关键一手依据**：[Cambridge 禁止历年试卷在网站发布](https://help.cambridgeinternational.org/hc/en-gb/articles/203544371-Can-I-reproduce-Cambridge-past-examination-papers-on-the-school-s-website-my-website)；[Cambridge 版权许可 FAQ](https://help.cambridgeinternational.org/hc/en-gb/articles/115004418469-How-do-I-apply-for-permission-to-use-Cambridge-copyrighted-material)；[校内教学使用与再发布边界](https://help.cambridgeinternational.org/hc/en-gb/articles/115004333125-Can-I-reproduce-Cambridge-past-examination-papers-for-use-in-the-classroom-with-my-students)。

Cambridge 的常规许可 FAQ 明确不授权历年题的电子出版、完整试卷和评分标准等内容；学校获得的内部教学使用许可不能直接转给面向公众的 LeetMath。对出版式商业用途可能还需根据具体项目联系考试局，但未经明确授权**不能**依赖“教育用途”推定商业再发布合法。

## 2. 站点政策分层（核验于 2026-11-18）

| 站点 | 自动化访问策略 | 来源 |
| --- | --- | --- |
| Save My Exams | **不抓取**；5.10 限制数据挖掘/网络抓取 | [Terms §5.10](https://www.savemyexams.com/us/terms/) |
| Dr Frost Learning | **不抓取**；网站条款限制 robots/scraping | [Website terms](https://www.drfrost.org/website-terms) |
| Maths Genie | **不抓取**；条款禁止机器人/数据提取及竞品数据集 | [Terms](https://mathsgenie.co.uk/terms) |
| Examaced | 在完成条款及许可复核前不抓取/不批量下载 | [站点](https://www.examaced.com/) |
| Exam-Mate / Momentum / PastPaper.cc | 需书面授权、清晰内容权属与自动化许可 | 各自公开站点，条款待逐项核对 |
| Cambridge / Pearson | 官方来源 URL 和人工索引；自动化仍需政策核验 | 官方页面与版权政策 |
| OPL GitHub | 许可范围内可 git clone 并做本地研究性索引 | [OPL_LICENSE](https://github.com/openwebwork/webwork-open-problem-library/blob/main/OPL_LICENSE) |

robots.txt 是技术访问规则，**不是著作权许可**。反过来，拿到了授权也应遵守合约、速率、账户和数据边界。

## 3. 爬虫设计：只为获准来源启用

研究原型推荐：
1. 在 Source Registry 记录 owner、domain、license、permission_document_id、allowed_paths、allowed_use、checked_at、expiry。
2. 在任务执行前运行可执行的策略检查：未获许可、过期、受限内容直接拒绝。
3. 默认同域、只读、深度/页数/并发/延迟上限；尊重 robots.txt 和站点条款。
4. 优先目录、分类、标题与官方 URL；**不默认下载 PDF、题干、答案或付费资源**。
5. 记录原始 URL、HTTP 状态、发现时间、内容哈希、归一化 ID、来源证据。
6. 发生 401/403/429、验证码、登录或防爬提示时停止，不绕过；无授权不借助代理或模拟登录规避限制。
7. 每批数据经内容审核、权利审查、题目去重、考纲审核，再决定内部研究或公开发布。

**最小 JSON 结构**（示意）：

~~~json
{
  "provider": "official_cambridge",
  "content_kind": "external_paper_index",
  "source_url": "https://www.cambridgeinternational.org/...",
  "qualification_code": "9709",
  "syllabus_version": "2026-2027",
  "year": 2026,
  "session": "June",
  "component": "1",
  "variant": null,
  "rights_status": "link_only",
  "publication_status": "metadata_only",
  "checked_at": "2026-11-18"
}
~~~

## 4. 文件类来源处理

对**已有合法处理权**的 PDF：

- PyMuPDF：文字/版面/图片/坐标；pdfplumber：表格或布局辅助。
- 多模态视觉解析：数学公式、函数图、解析几何/立体几何、多个小问；不依赖 OCR 作为唯一来源。
- 题目结构化：保留原页码、题号、分数、原图引用、文字与 LaTeX 双表示。
- SymPy/PG：验证表达式与数值；复杂过程/证明须人工复核。
- 阶段输出分为 raw_metadata、parsed_question、reviewed_question、publishable_question。

## 5. OPL 的非商业限制

[OPL 官方许可说明](https://wiki.openwebwork.org/wiki/Open_Problem_Library)：贡献者通常保留版权，默认 CC BY-NC-SA 3.0，可能有单独许可的题目/图片/宏。商业 LeetMath 不应未经授权直接引用其整题、翻译题、改编题，也不能因自建 Renderer 就绕过非商业许可；应单独核验每份作品、适用范围和衍生内容义务。

## 6. 发布门禁建议

- <code>rights_status=unknown/restricted</code> ⇒ 不得公开题目内容。
- <code>rights_status=link_only</code> ⇒ 只可公开来源链接及自写索引元数据。
- <code>rights_status=licensed</code> ⇒ 运行时校验许可有效期、地域、使用类型、授权对象。
- <code>rights_status=original</code> ⇒ 内容原创审查通过才能发布。
- <code>rights_status=revoked</code> ⇒ 下架并清除不应继续保存的衍生缓存。
- 题目审核与权利审核是两件事；**教师默认通过的偏好不能覆盖版权门禁**。

本页为工程合规基线，不替代司法辖区相关的专业法律审查。
