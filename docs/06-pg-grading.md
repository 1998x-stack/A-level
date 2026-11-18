# 06 · PG 参数化出题、MathObjects 判分与 A-Level 评分扩展

## 1. 核心范式

一题不等于一个固定题面：

~~~text
question_template (.pg / 原创 DSL)
      + source_sha + engine_version + problem_seed
                    ↓
             question_instance
          (题干、参数、答题空、图形)
                    ↓
              student_attempt
                    ↓
            answer_verification
                    ↓
        grading_evidence / review
~~~

LeetMath 应将 template、instance、attempt 分开。历史练习引用实例快照，复习时允许根据算法重新生成参数，但重放旧题必须固定源码与版本。

## 2. 原创 PG/PGML 简化模板

~~~perl
## DBsubject(Calculus - single variable)
## DBchapter(Differentiation)
## DBsection(Polynomial derivatives)
## KEYWORDS('derivative', 'polynomial')

DOCUMENT();

loadMacros("PGstandard.pl", "MathObjects.pl", "PGML.pl", "PGcourse.pl");
TEXT(beginproblem());
Context("Numeric");

$a = random(2,6,1);
$b = random(1,9,1);
$answer = Compute("2*$a*x+$b");

BEGIN_PGML
Let [`f(x) = [$a]x^2 + [$b]x + 3`].
Find [`f'(x)`].

[_________________]{$answer}
END_PGML

BEGIN_PGML_SOLUTION
By the power rule, [`f'(x)=2[$a]x+[$b]`].
END_PGML_SOLUTION

ENDDOCUMENT();
~~~

以上是**示意代码，尚未在 PG 真实引擎中执行验证**；需要依照固定 renderer 版本多 seed 测试，尤其检查 PGML 内插、数学排版与解析器语义。

示例导数：若 f(x)=4x²+5x+3，则 f'(x)=8x+5。同一数学题应能接受配置允许的等价表示，而不是直接做字符串比较。

## 3. MathObjects 支持的主要答案类别

| 输入类别 | 数学例子 | 对应候选对象 |
| --- | --- | --- |
| 数值与近似 | sqrt(2)、0.125 | Real / Numeric |
| 代数式 | (x+1)^2 | Formula / Compute |
| 复数 | 3+4i | Complex |
| 向量/矩阵 | (1,2,3)、矩阵 | Vector / Matrix |
| 集合/区间 | {1,2}、(-∞,3] | Set / Interval |
| 多答案条件 | 根、系数和步骤组合 | List / MultiAnswer |
| 多阶段题目 | scaffolded problem | Scaffold 等宏 |

详细用法以 [MathObjects 教程](https://wiki.openwebwork.org/wiki/Introduction_to_MathObjects) 和 [PGML](https://wiki.openwebwork.org/wiki/Introduction_to_PGML) 为准。

## 4. 数学等价的边界

Formula 的数值测试点比较是有用的，但不等于数学证明。可能遇到以下问题：

- (x²−1)/(x−1) 与 x+1 在 x≠1 等价，但定义域不同。
- sqrt(x²)=|x|，不能在全实数上简单当成 x。
- 1/x 与 x^(-1) 的奇点和允许区间需要明确。
- 小数近似需要容差、有效数字、单位规则。
- 数学题有时要求“展开式”“因式分解式”“精确形式”，即便数值相等也未必满足格式要求。

参考：[FormulaTestPoints](https://wiki.openwebwork.org/wiki/FormulaTestPoints)。

推荐判分顺序：安全语法解析 → 标准化 → 类型/格式/定义域 → PG MathObjects 数值/表达式检查 → SymPy 交叉检验（必要时）→ 多步评分规则 → 不确定案例交教师。

**不得用表达式相等替代证明过程正确。**

## 5. A-Level 分步得分的独立模型

Cambridge 常见评分标记：
- M：Method mark；
- A：Accuracy mark；
- B：Independent mark。

PG 可以做多答题空、MultiAnswer、Scaffold 与部分得分，但不能原生等同于某张 CAIE 评分标准。LeetMath 应保留：

~~~yaml
marking_scheme:
  board: CAIE
  qualification: "9709"
  syllabus_version: "2026-2027"
  total_marks: 6
  criteria:
    - id: c1
      mark: M1
      check_type: teacher_or_structured_method
    - id: c2
      mark: A1
      depends_on: c1
      check_type: symbolic
    - id: c3
      mark: B1
      check_type: independent_fact
  review_policy:
    low_confidence: teacher_required
~~~

这只是**原创评分规则示意**，不是官方 mark scheme 的转载。

## 6. 教师审核与机器评分

- 自动判分可以提供 provisional_score、rule_evidence、grader_version、uncertainty。
- 高风险题（数学证明、复杂几何、分步方法）必须允许教师覆核并保留审计记录。
- 教师端可以按组织策略默认标记“教师已确认”，但不能因此伪造看过的判分证据；版权/权限永远独立强校验。
- 学生端应该展示得分、原因、可接受形式及重做入口，不暴露未经授权的标准答案资源。

## 7. 验收基准

用自编/获许可的 200+ 数学判分样本覆盖：等价式、非等价式、定义域、精度/单位、格式限制、多答案顺序、极端参数 seed、M/A/B 依赖和故障恢复。额外构建容易误判的“反例”集合，统计误给分、误拒分、教师介入率，不能只统计平均准确率。
