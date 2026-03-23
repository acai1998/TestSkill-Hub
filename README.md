# TestSkill-Hub
测试技能中心 —— 面向测试人员的 AI Agent Skill 仓库

## 📦 技能列表

| Skill | 功能描述 | 适用场景 |
| --- | --- | --- |
| [prd-to-testcase](./prd-to-testcase/) | PRD/需求文档转测试用例，输出 Markdown 或 XMind 文件 | 需求评审后、迭代开始前 |
| [bug-report-writer](./bug-report-writer/) | 根据缺陷现象生成规范 Bug 单，含严重等级/复现步骤/环境信息 | 测试执行过程中 |
| [api-test-gen](./api-test-gen/) | 根据接口文档生成接口测试用例，覆盖正常/异常/鉴权/边界值 | 接口联调前后 |
| [test-report-writer](./test-report-writer/) | 根据测试结果生成测试报告，含质量评估和上线建议 | 版本发布前 |
| [regression-scope](./regression-scope/) | 根据变更内容分析回归范围，输出带优先级的回归测试矩阵 | Bug 修复/需求变更后 |

---

## 🚀 如何使用

所有 Skill 均在 **CatPaw IDE** 中通过 AI Agent 触发，无需手动执行脚本。

### 使用方式
1. 在 CatPaw 中打开对话，切换到对应 Skill 所在的工作目录
2. 直接用自然语言描述任务，Agent 会自动读取 `SKILL.md` 并执行
3. 产物文件会自动落地到对应的 `outputs/` 目录下

### 快速触发示例

**生成测试用例：**
```
根据这份需求文档生成测试用例并输出 md 文件：<需求文本或链接>
```

**写 Bug 单：**
```
帮我把这个 Bug 整理成规范的 Bug 单：<现象描述>
```

**生成接口测试用例：**
```
根据这份接口文档生成接口测试用例：<Swagger/YAPI 内容>
```

**生成测试报告：**
```
帮我生成测试报告，执行了 200 条用例，通过 185 条，发现 15 个 Bug，遗留 3 个 P2
```

**分析回归范围：**
```
这次改了用户登录模块的 Token 刷新逻辑，帮我分析回归范围
```

---

## ✅ 如何验证 Skill 效果

### 方法一：用示例输入快速验证

每个 Skill 的 `SKILL.md` 末尾都有「快速触发示例」，直接复制示例 prompt 发给 Agent 即可触发。

验证标准：
- Agent 是否按照工作流步骤执行（可观察对话过程）
- 是否在 `outputs/` 目录下生成了实际文件（**必须落地文件，不能只在对话中输出**）
- 产物内容是否符合对应的输出模板格式

### 方法二：用真实业务数据验证

用当前正在进行的真实项目数据触发 Skill，对比产物与人工产出的质量差距：

| Skill | 验证方法 | 验收标准 |
| --- | --- | --- |
| `prd-to-testcase` | 输入一份真实需求文档 | 用例覆盖功能/边界/异常，有文件落地 |
| `bug-report-writer` | 输入一个正在测的真实 Bug | 标题格式规范，步骤可独立复现，等级准确 |
| `api-test-gen` | 输入一个真实接口定义 | 每个接口有正常+异常+鉴权用例，有文件落地 |
| `test-report-writer` | 输入最近一次版本的测试数据 | 通过率计算正确，上线建议有数据支撑 |
| `regression-scope` | 输入最近一次 Bug 修复列表 | 回归矩阵有优先级分级，影响分析有依据 |

### 方法三：检查产物文件

Skill 执行完毕后，检查以下目录是否有新文件生成：

```
prd-to-testcase/outputs/testcases/
bug-report-writer/outputs/reports/
api-test-gen/outputs/testcases/
test-report-writer/outputs/reports/
regression-scope/outputs/analysis/
```

---

## 📁 目录结构说明

每个 Skill 遵循统一目录规范：

```
<skill-name>/
├── SKILL.md              # 技能定义文件（Agent 入口，必读）
├── rules/                # 规则文件目录
│   ├── _sections.md      # 规则索引（说明何时读哪个规则）
│   └── *.md              # 各专项规则
└── outputs/              # 产物落地目录（自动生成，无需手动操作）
```
