# teach-mode

把 coding agent 从"替你做完"变成"教你学会"。思路借鉴自 [amosblomqvist/learn](https://github.com/amosblomqvist/learn)，这里改写成一个 [STYLE](https://github.com/inxups/style-cli) 规则包，可以装进 Claude Code 和 Codex。

这是我给自己用的一套规则，按原样分享。

## 它怎么工作

琐碎任务（改拼写、跑一条已知命令）直接做，并说明跳过了教学。其余任务分三个阶段，不能跳过也不能合并：

1. **摸底**：用能判对错的测验题定位你会与不会的边界，不问"你了解 X 吗"。答对就加难，答错就收窄，最后产出一张 ✓会 / ✗不会 / ?待定 的知识清单。
2. **计划**：问清学习目标，给出讲解顺序和一张依赖图，根部是无需证明的基础事实，末端是目标。你确认后才开始执行。
3. **执行**：边做边讲。碰到 ✗ 或 ? 节点时，按 动机 → 建立 → 连接 → 检查 四部分讲完，再出一道题；答对才继续做依赖它的那一步。

每道测验题都带"不知道"选项，干扰项针对具体误解设计；作答后立即给出 ✓/✗、正确答案和一句解释。

## 包含什么

- `STYLE.yaml`：包声明、`language` 设置（默认 `zh-CN`）和常驻 token 预算
- `rules/core.md`：教学理念和 摸底 → 计划 → 执行 流程
- `rules/quiz.md`：出题规范
- `rules/quiz-guard.md`：Claude Code 专用，每次调用 AskUserQuestion 前复核选项有没有泄露答案
- `rules/quiz-codex.md`：Codex 专用，测验改为在正文里编号出题
- `rules/turn-codex.md`：Codex 专用，每轮提醒当前阶段，避免长会话里流程被稀释

## 安装

需要先装好 [STYLE CLI](https://github.com/inxups/style-cli)。然后从 Git 安装：

```bash
style install 'git+https://github.com/inxups/inxv-styles.git//teach-mode?ref=refs/heads/main'
```

默认装到检测到的所有 harness。只装到某一个，或者换讲解语言：

```bash
style install 'git+https://github.com/inxups/inxv-styles.git//teach-mode?ref=refs/heads/main' --harness claude-code --set language=en
```

只想在某个学习项目里启用，在该项目根目录运行：

```bash
style --project . install 'git+https://github.com/inxups/inxv-styles.git//teach-mode?ref=refs/heads/main'
```

本地修改规则时，用 `style link ./teach-mode` 链接，改完运行 `style sync` 生效。

## 要求

- [STYLE CLI](https://github.com/inxups/style-cli)
- Claude Code 或 Codex。Claude Code 下测验通过 AskUserQuestion 弹窗出题；Codex 下在正文里出题。

## 说明

- 这套规则只有一个学习者（我）。按你自己最有效的学法改 `rules/core.md`。
- 原版 learn 还有研究员子 agent（查证事实）和图示生成；这里没有，改为要求 agent 对拿不准的事实先搜索、读源码或读文档再讲。
- `core.md` 较长，常驻预算设为 1300 tokens。改规则后可用 `style plan ./teach-mode` 查看各 harness 的映射和 token 开销。
