# 大模型是怎么造出来的（agent 技能）

一个给 AI agent 用的技能包：**把大模型的训练流程讲清楚，并且能判断关于模型的宣称是否可信。**

内容来自 Yann Dubois 在斯坦福 CS229 的客座课 *Building Large Language Models*（2024 夏）。技能里的每条结论都对应 `references/lecture-notes.md` 里的时间码，可以回查，不需要盲信。

## 为什么做这个

多数"大模型原理"的介绍停在"Transformer + 注意力"。真正决定模型好坏的——数据管线、后训练配方、评测方法、系统优化——通常被一句带过。

这个技能补的正是这几块，而且做成 agent 能直接用的形式：

- **写内容**：一套不空泛的 AI 解说骨架（五部件 → 预训练 → 后训练 → 评测）。
- **做判断**：三个问题拆掉大部分榜单话术——用什么基准、和谁比、裁判是谁。
- **做评估**：解释为什么"拿业务文档微调一下"通常拿不到想要的效果。

## 目录

```
SKILL.md                          # 技能本体
references/lecture-notes.md       # 证据表：结论 → 原课时间码（英文）
references/lecture-notes.zh-CN.md # 同内容中文版
```

## 安装

**Claude Code / Claude Skills**

```bash
git clone https://github.com/lizhao888/llm-building-map.git
mkdir -p ~/.claude/skills
cp -r llm-building-map ~/.claude/skills/llm-building-map
```

**Hermes Agent**

```bash
cp -r llm-building-map ~/.hermes/skills/research/llm-building-map
# Windows: %LOCALAPPDATA%\hermes\skills\research\llm-building-map
```

**其他读 `SKILL.md` 的 agent**：把目录放到它的技能目录即可；frontmatter 用通用的 `name` + `description` 约定。

## 一分钟版本

| 部件 | 决定什么 | 谁在卷 |
|------|----------|--------|
| 架构 | 容量与形态 | 学界 |
| 训练目标 | 训练时"好"的定义 | 学界 |
| **数据** | 模型见过什么 | 工业界 |
| **评测** | 你能否判断它变好了 | 工业界 |
| **系统** | 训练跑不跑得完 | 工业界 |

预训练把互联网压成一个下一词模型；后训练把它变成助手——SFT 教格式与人格（不教知识），RLHF/DPO 对齐偏好。大部分"效果提升"的说法，坏在评测这一环。

## 说明

- 非官方学习笔记，与斯坦福、Anthropic 及讲者无关。
- 课程是 2024 年口径；更长上下文、推理模型、合成数据管线等不在范围内，文中已标注。
- 讲者原话里带保留的地方（"我觉得""我不确定"），笔记保留保留，不升级成事实。

## 许可

MIT，见 [LICENSE](LICENSE)。
