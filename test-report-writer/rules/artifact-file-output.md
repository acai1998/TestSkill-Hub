# 产物文件输出规范

## 核心原则
**所有结果必须落地为文件，不能只在对话页面展示。**

## 输出路径规范
- 测试报告统一输出到：`outputs/reports/`
- 文件命名格式：`<product>-test-report-<YYYYMMDD>.md`
  - 例：`order-v2.3-test-report-20260323.md`
  - 例：`user-center-test-report-20260323.md`

## 强制要求
1. 每次执行结束必须给出完整文件路径
2. 文件路径使用相对路径（相对于 skill 根目录）
3. 不得只在对话中输出内容而不写文件
