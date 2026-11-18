# 08 · LeetMath A-Level 数据模型与数据库迁移建议

**目标**：共享 LeetMath 的数学基础设施，同时独立保存考试局、考纲版本、试卷组合、授权、参数化实例、分步评分和学生复习记录。以下是逻辑草案，不能未经对照现有 LeetMath schema 直接执行。

## 1. 数据关系

~~~text
exam_boards ─ qualifications ─ syllabus_versions ─ exam_components
                                 ├─ syllabus_topics
                                 └─ award_pathways
exam_components ─ exam_papers ─ paper_question_refs
questions ─ question_parts ─ marking_rules
   ├─ source_records / rights_grants
   ├─ question_topic_links ─ syllabus_topics
   ├─ question_templates ─ question_instances
   └─ attempts ─ submitted_answers ─ grading_evidence ─ teacher_reviews
students ─ mastery_records / review_schedule
schools ─ class_assignments / tenant_private_question_bank
~~~

## 2. 核心实体

| 实体 | 关键字段 | 作用 |
| --- | --- | --- |
| exam_boards | code, name | CAIE、PEARSON、OXFORD_AQA |
| qualifications | board_id, qualification_code, level | 9709、9231、IAL 等 |
| syllabus_versions | qualification_id, version_label, exam_year_from/to, source_url | 版本审计 |
| exam_components | syllabus_id, code, title, max_marks | P1、P3、M1 |
| award_pathways | syllabus_id, award_level, rule_json | 有效 AS/A-Level 组合 |
| exam_papers | component_id, year, session, variant, revision, status | 精确试卷实例 |
| source_records | provider, source_url, content_sha256, checked_at | 溯源 |
| rights_grants | source_id, rights_status, scope_json, expires_at, evidence_url | 发布门禁 |
| questions | id, kind, content, visibility, rights_id, status | 题目 |
| question_parts | question_id, parent_id, part_key, marks | 树形小问 |
| marking_rules | part_id, mark_code, points, depends_on, validation_type | M/A/B/条件依赖 |
| question_templates | question_id, engine, source_sha, engine_version | PG 或原创模板 |
| question_instances | template_id, problem_seed, renderer_digest, snapshot | 历史重放 |
| attempts | user_id, instance_id, submitted_at, status | 作答 |
| submitted_answers | attempt_id, part_id, raw, normalized | 多空答案 |
| grading_evidence | attempt_id, rule_id, grader_version, score, payload | 判分证据 |
| teacher_reviews | attempt_id, reviewer_id, decision, reason | 教师覆核 |
| syllabus_topics | syllabus_id, code, name_zh/en, parent_id | 考纲标签 |
| topic_mappings | external_topic, target_topic_id, confidence, review_status | OPL 映射 |
| mastery_records / review_schedule | user_id, topic_id, due_at, ease, score | Anki 式复习 |

## 3. PostgreSQL 结构草案

~~~sql
CREATE TABLE exam_boards (
  id BIGSERIAL PRIMARY KEY,
  code TEXT NOT NULL UNIQUE,
  display_name TEXT NOT NULL
);
CREATE TABLE qualifications (
  id BIGSERIAL PRIMARY KEY,
  board_id BIGINT NOT NULL REFERENCES exam_boards(id),
  code TEXT NOT NULL,
  level TEXT NOT NULL,
  UNIQUE (board_id,code,level)
);
CREATE TABLE syllabus_versions (
  id BIGSERIAL PRIMARY KEY,
  qualification_id BIGINT NOT NULL REFERENCES qualifications(id),
  label TEXT NOT NULL,
  exam_year_from INT NOT NULL,
  exam_year_to INT,
  source_url TEXT NOT NULL,
  UNIQUE (qualification_id,label)
);
CREATE TABLE exam_components (
  id BIGSERIAL PRIMARY KEY,
  syllabus_id BIGINT NOT NULL REFERENCES syllabus_versions(id),
  code TEXT NOT NULL,
  title TEXT NOT NULL,
  UNIQUE (syllabus_id,code)
);
CREATE TABLE source_records (
  id UUID PRIMARY KEY,
  provider TEXT NOT NULL,
  source_url TEXT NOT NULL,
  content_sha256 CHAR(64),
  checked_at TIMESTAMPTZ NOT NULL DEFAULT now()
);
CREATE TABLE rights_grants (
  id UUID PRIMARY KEY,
  source_id UUID NOT NULL REFERENCES source_records(id),
  rights_status TEXT NOT NULL CHECK (
    rights_status IN ('unknown','link_only','restricted','licensed','original','revoked')
  ),
  allowed_uses JSONB NOT NULL DEFAULT '{}'::jsonb,
  evidence_url TEXT,
  expires_at TIMESTAMPTZ
);
CREATE TABLE question_templates (
  id UUID PRIMARY KEY,
  question_id UUID NOT NULL,
  engine TEXT NOT NULL,
  source_sha256 CHAR(64) NOT NULL,
  engine_version TEXT NOT NULL
);
CREATE TABLE question_instances (
  id UUID PRIMARY KEY,
  template_id UUID REFERENCES question_templates(id),
  seed BIGINT,
  renderer_digest TEXT,
  payload JSONB NOT NULL,
  created_at TIMESTAMPTZ NOT NULL DEFAULT now()
);
~~~

以上 SQL 只是增量模型示例，缺少尚未创建的 questions 等表外键，是**设计参考**而非可运行完整 migration。

## 4. 约束与注意

- 考纲版本是主要隔离维度；同卷号可能在不同考试局或版本含义不同。
- 对同一 year/session/variant 的替换卷保留独立 revision/status，不能覆盖历史。
- 小问树必须保存 marks、评分依赖；教师复核采用追加式审计，不覆盖原机器结果。
- 权利状态 unknown/restricted/revoked 必须从后端阻断公开内容。
- PG 题实例固定 source_sha、seed、renderer_version、必要的 snapshot；仅保存 seed 不足以历史重放。
- 校园版 tenant_id 隔离校内授权题与学生数据。
- 概念映射是多对多：同一道原创题可关联上海高考、9709、Edexcel 的不同知识点，须分别审核。

## 5. 迁移顺序

先阅读 LeetMath 当前数据库 schema → 做实体对照/兼容表 → 增加课程与 rights 维度 → 加模板/实例与评分证据 → 补强报表和复习字段。必须准备回滚方案、旧题兼容测试和权限回归测试。
