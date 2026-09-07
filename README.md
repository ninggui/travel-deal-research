# 出行比价盯价

![GitHub stars](https://img.shields.io/github/stars/ninggui/travel-deal-research)
![License](https://img.shields.io/github/license/ninggui/travel-deal-research)
[![SkillHub](https://img.shields.io/badge/SkillHub-在线安装-blue)](https://skillhub.cn/skills/travel-deal-research)

机票/酒店/火车比价与盯价：多平台对比、低价日历、降价监控。

## 这是什么

一个可复用的 AI Agent 技能（Skill），来自真实业务场景沉淀，含完整执行流程、避坑清单与验证步骤。

## 快速使用

将本仓库放入 Agent 技能目录后，用对应触发词调用（见 SKILL.md），Agent 会自动加载并执行完整流程。

## 核心能力

| 能力 | 说明 |
|------|------|
| 机票多平台比价 |
| 酒店搜索比价 |
| 降价监控提醒 |
| 往返组合最优计算 |
| 特殊线路专项策略 |

## 使用方式（安装）

- **Hermes**: 放入 `skills/` 目录
- **Claude**: 放入 `~/.claude/skills/`
- **其他 Agent**: 按对应 SKILL.md 格式放入技能目录
- **SkillHub 一键安装**: https://skillhub.cn/skills/travel-deal-research

## 优势

- 真实比价数据（非估算）
- 可设阈值自动盯价
- 覆盖机场往返/高成本线路

## 内容结构

- `SKILL.md` — 核心技能定义（触发条件、执行流程、避坑清单）
- `references/` — 可选参考文件

## 许可

MIT
