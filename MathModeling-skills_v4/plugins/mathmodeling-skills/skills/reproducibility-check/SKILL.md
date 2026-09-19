---
name: reproducibility-check
description: 在干净环境重跑所有代码，确认论文中的数值与实际输出一致；检查随机种子、依赖版本、数据路径。
---

# Purpose

数学建模论文最容易被评委发现的硬伤就是"论文写的和代码跑的不一致"。
本 skill 在论文提交前自动执行可复现性检查，确保：
1. 代码在干净环境能从头跑通
2. 论文中的每个数值都能在代码输出中找到
3. 随机种子固定、依赖版本明确、数据路径正确

# Checks

## 1. 干净环境重跑
- 清空 results/ 目录下的旧输出（保留输入数据）
- 按 code/Q1~Qx/ 的顺序重新执行所有脚本
- 检查每个脚本是否正常退出（exit code=0）
- 如果某脚本报错：
  - 检查是否有未安装的依赖
  - 检查是否有硬编码的绝对路径
  - 检查是否有 except: pass 吞掉了错误
  - 检查是否有 break 导致实验没真跑

## 2. 随机种子检查
- 搜索所有 Python 文件中的 `random_state`、`np.random.seed`、`torch.manual_seed`
- 确认所有随机种子统一为 454520（除非有明确理由）
- 如果某段代码没有设种子，结果不可复现，标记为 WARNING

## 3. 依赖版本检查
- 生成 requirements.txt（pip freeze）
- 检查关键库版本：pandas、numpy、scikit-learn、statsmodels、networkx
- 如果论文中引用了某个库的特定功能（如 SARIMA、DBSCAN），确认版本支持

## 4. 数据路径检查
- 所有脚本不得使用绝对路径（如 /home/user/...）
- 数据路径应为相对路径（如 workspace/data_raw/...）
- 检查 workspace/data_raw/ 下的原始数据是否未被修改

## 5. 输出一致性检查
- 重跑后，用 data-vs-paper-check skill 自动比对论文数值与新输出
- 如果数值不一致：
  - 如果是随机种子导致的微小差异，确认在可接受范围内
  - 如果是结构性差异（数值完全不同），说明代码或论文有误，必须修复

# Output

生成 `results/reproducibility_check_report.md`，包含：
- 每个脚本的重跑结果（通过/失败/警告）
- 随机种子检查结果
- 依赖版本清单
- 数据路径问题列表
- 数值一致性比对结果

# Workflow

1. 备份现有 results/ 到 results_backup/
2. 清空 results/ 下的输出文件（保留目录结构）
3. 按顺序执行所有代码脚本
4. 收集每个脚本的 stdout/stderr/exit code
5. 检查随机种子和依赖
6. 执行 data-vs-paper-check 比对数值
7. 生成报告
8. 如果全部通过，标记 REPRODUCIBILITY_PASSED；
   如果有失败项，列出必须修复的问题
