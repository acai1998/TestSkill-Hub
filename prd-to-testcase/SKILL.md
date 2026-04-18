---
name: 测试用例生成器
description: 从需求文档、PRD或技术方案中生成结构化测试策略与测试用例，并将结果落地为 Markdown 或 XMind 文件（支持文本、本地文件、公开 URL、双文档输入）。适用于"根据需求生成测试用例""需求转测试点""补齐测试覆盖""输出测试计划/脑图"等场景。
---

# 需求测试用例生成器（通用版）

## 技能介绍
这个技能用于将需求文档、PRD、技术方案或业务描述，快速转为可执行、可评审的测试策略与测试用例。

核心目标：
- 覆盖完整（功能、边界、异常、兼容性；安全/性能按需补充）
- 结构清晰（便于评审、执行、回归）
- 结果可落地（必须产出文件 + metadata，而不是仅页面展示）
- 可追踪（每次生成留有元数据记录，支持迭代改进）

## 何时应用
- 用户要求"根据需求生成测试用例"。
- 用户要求"输出测试策略/测试计划/覆盖矩阵"。
- 用户要求"导出 Markdown 文档"或"生成 XMind 脑图"。
- 用户提供 PRD + 技术方案，要求双文档联合生成用例。

## 必须先读的规则
执行本技能时，先阅读：
- `rules/_sections.md`

再根据任务读取对应规则：
- 产物输出：`rules/artifact-file-output.md`
- 覆盖维度：`rules/coverage-multi-dimension.md`
- 用例质量：`rules/quality-atomic-steps.md`
- 覆盖追踪：`rules/trace-requirement-mapping.md`
- XMind 结构：`rules/xmind-structure.md`
- 元数据规范：`rules/output-metadata.md`

## 产出目标（强制）
1. 不能只在对话页面输出结果，必须落地为文件。
2. 默认输出路径格式（分层结构）：
   ```
   outputs/
   └── <YYYY-MM>/
       └── <project-slug>/
           └── <YYYYMMDD>_v<N>/
               ├── testcases.md        ← 测试用例主文档
               ├── metadata.json       ← 生成元数据（强制）
               └── testcases.xmind     ← 仅用户要求时生成
   ```
3. 示例路径：
   - `outputs/2026-04/order-submit/20260418_v1/testcases.md`
   - `outputs/2026-04/order-submit/20260418_v1/metadata.json`
4. 当用户提到"XMind/脑图/思维导图"时，额外产出 `testcases.xmind`。
5. 若用户只要 XMind，仍需先生成 Markdown，再转换为 XMind。
6. 最终回复中必须明确给出产物路径（所有文件）。

## 输入模式
- 单文档：仅 PRD/需求文档。
- 双文档：需求文档 + 技术方案/接口文档。
- 支持输入来源：文本、本地文件摘录、公开 URL。

## 执行原则
1. 先理解业务目标、角色、核心流程，再写用例。
2. 信息不完整时，先基于合理假设继续产出，并显式列出"待确认项"。
3. 用例必须覆盖：功能、边界、异常；兼容性、性能、安全按需覆盖（以需求文档为准）。
4. 每条用例必须可执行、可验证，避免"系统正常"这类模糊描述。
5. 产物必须满足规则文件中的格式和质量要求。
6. 每次生成必须产出 `metadata.json`，记录生成上下文。

## 标准工作流
复制以下清单并逐步完成：

```text
Task Progress:
- [ ] Step 1: 解析输入并抽取需求点
- [ ] Step 2: 划分功能模块与端到端场景
- [ ] Step 3: 识别风险并定义测试维度
- [ ] Step 4: 生成冒烟(P0)与完整测试用例
- [ ] Step 5: 写入 Markdown 产物
- [ ] Step 6: 写入 metadata.json
- [ ] Step 7: （可选）转换为 XMind 产物
```

### Step 1: 解析输入并抽取需求点
- 提取：功能目标、用户角色、业务规则、约束条件、异常路径。
- 若为双文档输入：建立需求点与技术实现点映射（接口、状态流、权限、数据校验）。

### Step 2: 划分模块与场景
- 按"功能模块 -> 业务场景 -> 关键动作"分层。
- 先列主流程，再补充分支、回退、失败恢复场景。

### Step 3: 风险与测试维度
核心覆盖维度（必须）：
- 功能正确性
- 边界与等价类
- 异常与容错

按需覆盖维度（以需求是否涉及为判断标准）：
- 权限校验（需求中有角色/权限差异时）
- 兼容性（需求中有端/浏览器/版本要求时）
- 性能（需求包含 SLA/高并发指标时）

> 注意：安全渗透、SQL 注入等专项安全测试不在本技能范围内，不主动生成此类用例。

### Step 4: 生成用例
按"主流程 -> 分支 -> 异常 -> 边界"顺序组织，优先保证可执行性和可验证性。

### Step 5: 写入 Markdown 产物
- 目标路径：`outputs/<YYYY-MM>/<project-slug>/<YYYYMMDD>_v<N>/testcases.md`
- 文件内容使用"输出模板（默认）"。
- 对话中仅做摘要，并给出文件路径。

### Step 6: 写入 metadata.json
生成 `metadata.json` 与 `testcases.md` 放在同一目录，记录以下信息：

```json
{
  "project": "<project-slug>",
  "generated_at": "<ISO8601时间>",
  "version": "v1",
  "input_sources": [
    { "type": "prd", "source": "<文件路径或URL>" },
    { "type": "tech_spec", "source": "<文件路径或URL>" }
  ],
  "output_stats": {
    "total_testcases": 0,
    "smoke_cases": 0,
    "functional_cases": 0,
    "boundary_cases": 0,
    "exception_cases": 0
  },
  "pending_items": 0,
  "reviewer": "",
  "review_date": ""
}
```

- `output_stats` 中各数字在用例生成后填入实际计数。
- `reviewer` 和 `review_date` 由人工评审后补填，不需 AI 生成。

### Step 7: 转换为 XMind 产物（可选）
当用户要求 XMind 时，执行：

```bash
python3 scripts/markdown_to_xmind.py \
  --input outputs/<YYYY-MM>/<project-slug>/<YYYYMMDD>_v<N>/testcases.md \
  --output outputs/<YYYY-MM>/<project-slug>/<YYYYMMDD>_v<N>/testcases.xmind \
  --title "<文档标题>"
```

- 转换后在回复中同时提供 Markdown 和 XMind 路径。

## 输出模板（默认）
写入 Markdown 文件时使用以下结构：

```markdown
## 一、测试策略
- 测试范围：
- 不测范围：
- 关键风险：
- 测试方法（功能/接口/UI/兼容/性能——按需标注）：
- 测试环境与数据要求：

## 二、冒烟用例
| 模块 | 场景 | 前置条件 | 步骤 | 预期结果 |
| --- | --- | --- | --- | --- |

## 三、完整测试用例
| 模块 | 测试点 | 前置条件 | 测试步骤 | 预期结果 |
| --- | --- | --- | --- | --- |

## 四、覆盖与追踪
| 需求点 | 覆盖说明 |
| --- | --- |

## 五、待确认项
- [ ] ...
```

## 质量门槛
- 每条用例至少包含：前置条件、明确步骤、可验证预期。
- 步骤使用动词开头，避免一句话包含多个断言。
- 预期结果可观察、可比对（页面字段、状态码、数据库状态、日志关键字等）。
- 覆盖矩阵中每个核心需求点至少映射 1 条用例。
- 结果必须落地到文件路径（含 `metadata.json`），不能只停留在会话中。

## 快速触发示例
- "根据这份需求文档生成测试用例，并输出 md 文件：<需求文本或链接>"
- "我有需求文档 + 技术方案，帮我出测试策略和完整用例，落到文件里。"
- "把下面需求转成可执行测试用例，并生成 xmind 脑图。"
