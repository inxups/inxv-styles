---
target: tool
names: AskUserQuestion
at: before
action: inject
repeat: turn
reason: 摸底阶段会连续出题，每轮复核一次选项是否泄露答案
when:
  harness:
    name: claude-code
---
如果这是测验题，提交前逐项复核：选项都不带理由；正确选项没有"(Recommended)"、单独加粗或更长更具体等形状差异；包含"不知道"选项；等用户作答后再判分和讲解。
