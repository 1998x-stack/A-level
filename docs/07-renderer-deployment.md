# 07 · Standalone PG Renderer 部署、HTTP API 与安全基线

**状态**：基于官方 README 的实施方案，尚未在本仓库执行真实 Docker 构建与渲染；不得把示例命令视为通过的生产部署记录。

## 1. 为什么用独立 Renderer

完整 WeBWorK2 自带学校课程与作业管理，但 LeetMath 已有主要业务流程。单独 [PG Renderer](https://github.com/openwebwork/renderer) 更适合验证题目渲染、数学答案校验和随机种子重放。生产中应只暴露 LeetMath API，Renderer 位于无公网入口的内部网络。

~~~text
Student Browser
    → LeetMath Web / Backend (auth, rights, attempt state)
    → PG Adapter (allowlist template, seed, limits)
    → Standalone Renderer (isolated container)
    → structured grade evidence
    → LeetMath DB / teacher review
~~~

## 2. 官方 Docker 开发实验

~~~bash
mkdir -p webwork-lab/volumes
cd webwork-lab

git clone --depth 1 \
  https://github.com/openwebwork/webwork-open-problem-library \
  volumes/webwork-open-problem-library

git clone --recursive \
  https://github.com/openwebwork/renderer container

docker build -t leetmath-pg-renderer:dev ./container

docker run -d \
  --name leetmath-pg-renderer \
  --publish 127.0.0.1:3000:3000 \
  --mount type=bind,source="$(pwd)/volumes/webwork-open-problem-library",target=/usr/app/webwork-open-problem-library \
  --env MOJO_MODE=development \
  leetmath-pg-renderer:dev
~~~

官方示例：[Renderer README](https://github.com/openwebwork/renderer)。开发服务通常可通过 http://127.0.0.1:3000 访问；macOS Apple Silicon 上的镜像构建、宏依赖、网络/权限仍需真实验证。

**生产前必须做**：固定 Git commit、切 production config、移除开发 Editor/写入接口、只读挂载、收紧 egress、无 root、CPU/memory/PID/time 限制、请求体上限、审计日志。若 Renderer 无法满足受限执行要求，不可直接向公众提供任意源码渲染。

## 3. 官方渲染 API

Renderer README 描述默认 <code>POST /render-api</code>，需要题目来源（problemSource、sourceFilePath 或 problemSourceURL 中一种）及 problemSeed。

| 字段 | 用途 | LeetMath 安全约束 |
| --- | --- | --- |
| problemSource | 原始 PG | 仅可信构建产物/审核后私有源码 |
| sourceFilePath | 文件引用 | 只允许内部 allowlist，不接收路径穿越 |
| problemSourceURL | 远端 PG 载入 | **对学生请求禁用**，避免 SSRF/不受信源码 |
| problemSeed | 随机实例 | 服务端生成并持久化 |
| _format=json | 结构化响应 | 与具体版本 schema 适配 |
| displayMode=MathJax | 数学排版 | 输出做内容安全检查 |
| showSolutions | 是否含解答 | 服务器端权限判断 |
| showHints | 是否含提示 | 按练习规则控制 |

本地请求示例（在确有 example.pg 且已运行容器时）：

~~~bash
curl --fail-with-body -sS -X POST \
  http://127.0.0.1:3000/render-api \
  --data-urlencode "problemSource@example.pg" \
  --data-urlencode "problemSeed=1234" \
  --data-urlencode "_format=json" \
  --data-urlencode "displayMode=MathJax" \
  --data-urlencode "showSolutions=0" \
  -o render.json
~~~

官方也描述 problemJWT / sessionJWT / answerJWT；这些与 token 的签发、校验必须遵循实际版本文档，不能自行假设响应字段或把内含答案信息交给未授权前端。

## 4. LeetMath 建议业务 API（非 WeBWorK 自带）

~~~http
POST /api/v1/questions/{id}/instantiate
POST /api/v1/attempts/{id}/submit
GET  /api/v1/attempts/{id}/feedback
~~~

- instantiate：校验来源权限、选取模板、生成 seed、保存模板 SHA/实例快照。
- submit：学生提交 answer_fields，由后端调用 PG adapter；结果不可由浏览器自行声明。
- feedback：返回必要反馈、按小问 score/marking evidence；依据权限决定是否展示标准解。
- PG 不可用：标记 pending_retry，不自动判 0 分；保证幂等、超时和重试策略。

## 5. 威胁模型

PG 本质上是可执行的 Perl/宏逻辑，不是安全 JSON。攻击面包括：不可信题目源码、路径与 URL 注入、任意外联、CPU/内存耗尽、返回 HTML/JS 中的脚本、答案或 JWT 泄露、缓存跨用户复用、依赖包供应链。

最低控制：
1. 模板源码经 Git 审核 + 签名/哈希固化，不执行学生自上传 PG。
2. Backend 到 Renderer 有身份验证/网络隔离；Renderer 无公网映射。
3. 禁止用户控制远程 URL、文件路径、源码、私有宏。
4. 非 root / drop capabilities / seccomp / 只读文件系统 / 临时目录限制 / 网络最小权限。
5. 最大 CPU、内存、文件大小、渲染时间、并发限制。
6. 缓存按 template_sha+engine_sha+seed+授权上下文区分，不缓存他人作答密钥。
7. 保留 request_id、template_sha、seed、status、error_class、评分版本；日志不储存敏感 JWT 明文。

## 6. 真实部署的停止条件

出现任意 untrusted PG 执行、外网访问不受控、解答泄漏、容器越权、重放不一致、严重错判或版权不清，则停止上线，回退到 LeetMath 原生 SymPy 验证或教师复核。

## 7. 第一批 smoke tests

固定 seed/多 seed、数值/表达式/向量/区间/多空比较、错误答卷、渲染中文/LaTeX、后台超时恢复、只读镜像、未授权模板访问、对外端口检查、日志/缓存泄密测试。

正式集成后的基准记录应附 image digest、Git commit、PG 宏版本、测试日志、案例集与复现命令。
