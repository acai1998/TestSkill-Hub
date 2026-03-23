---
title: 产物必须落地文件
impact: 关键
impactDescription: 防止结果只停留在会话页面
tags: artifact, markdown, xmind, output
---

## 产物必须落地文件

测试策略和测试用例必须写入本地文件，不能只在会话中展示。

**错误（只给页面结果，无文件）：**

```markdown
## 一、测试策略
...
```

**正确（先落地文件，再做摘要）：**

```text
输出文件：
- outputs/testcases/payment-refund.md
- outputs/testcases/payment-refund.xmind

会话仅展示摘要与文件路径。
```

### 最低要求

- 默认必须产出 1 份 Markdown：`outputs/testcases/<slug>.md`
- 用户明确要求“XMind/脑图”时，额外产出：`outputs/testcases/<slug>.xmind`
- 若只要求 XMind，也要先生成 Markdown 再转换
