# travel-deal-research

![GitHub stars](https://img.shields.io/github/stars/ninggui/travel-deal-research)
![License](https://img.shields.io/github/license/ninggui/travel-deal-research)
[![SkillHub](https://img.shields.io/badge/SkillHub-在线安装-blue)](https://skillhub.cn/skills/travel-deal-research)

机票 / 酒店 / 火车比价与盯价技能：多平台实时比价、低价日历、价格监控提醒。含往返组合计算与特殊线路处理。

## 快速使用

```bash
# 技能放入 Agent skills 目录后，直接触发：
"查 北京到上海 下周的机票价格"      # 多平台比价
"盯住 武汉到三亚 国庆往返的最低价"   # 降价监控
"对比一下这两家酒店的周末价格"       # 酒店比价
"拉萨线怎么买最划算"                # 特殊线路组合计算
```

## 核心能力

| 能力 | 说明 |
|------|------|
| 机票比价 | 多平台实时价格对比、低价日历 |
| 酒店比价 | 按城市/酒店名搜索比价 |
| 火车票 | 车次与票价查询 |
| 降价监控 | 目标价格监控，跌到阈值提醒 |
| 往返组合 | 去回程不同航司/平台组合最优价 |
| 特殊线路 | 高成本线路（如拉萨）专项组合策略 |

## 盯价机制

- 设置目标价阈值，周期检查，触发后推送提醒
- 支持多航线并行监控
- 价格数据来源为公开可访问渠道，无需登录

## 安装

- **Hermes**: `skills/` 目录
- **Claude**: `~/.claude/skills/`
- 或 SkillHub 一键安装：https://skillhub.cn/skills/travel-deal-research

## 内容结构

- `SKILL.md` — 核心技能定义
- `references/` — 线路定价与组合参考

## 许可

MIT
