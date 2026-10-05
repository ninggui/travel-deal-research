<img src="./assets/cover.png" alt="出行比价盯价" width="100%">

<div align="center">

# 出行比价盯价

**机票酒店多平台比价 + 往返组合 + 盯价，红眼航班要算住宿。**

![Status](https://img.shields.io/badge/status-production-green)
![Platforms](https://img.shields.io/badge/source-%E9%A3%9E%E7%8C%AB%2B%E6%90%BA%E7%A8%8B-blue)
![Lhasa](https://img.shields.io/badge/lhasa-%E6%90%BA%E7%A8%8B%E4%B8%93%E7%94%A8-red)
![Delay](https://img.shields.io/badge/anti--limit-2--5s%E9%9A%8F%E6%9C%BA-green)
![License](https://img.shields.io/badge/license-MIT-blue)

[它解决什么问题](#它解决什么问题) - [为什么比手动强](#为什么比手动强) - [工作流](#工作流) - [实测参数](#实测参数) - [快速开始](#快速开始)

</div>

---

## 它解决什么问题

订机票酒店只看单程裸价，红眼航班凌晨到还得多住一晚供氧酒店，亏的钱比省的多。小众航线（如拉萨）飞猪根本没数据，不换源就以为没票。这套打法把往返中转组合和"时间 + 住宿"综合成本一起算。

## 为什么比手动强

| 裸比单程价 | 本仓库 |
|---|---|
| 只看一段机票 | 往返中转 4 段组合算含税总价排序 |
| 红眼当便宜 | 把多住一晚住宿成本算进去再比 |
| 飞猪空就以为没票 | 飞猪无数据自动转携程源 |
| 人工连点触发反爬 | 2-5s 随机延时 + 失败退避 |

## 工作流

```
测可行中转枢纽
  → 分4段拉低价日历(去/回 × 去程段/回程段)
  → 组合计算含税总价(枢纽对 × 日期差7-8天)
  → 排序取 Top 组合 + 综合时间住宿对比
```

## 实测参数

- **成都→拉萨**低价日历实测 ¥310-420；**飞猪对拉萨方向全线 0 航班**，必须走携程源
- **Z264 武昌→拉萨** 硬卧 ¥741 / 41h56m；红眼多住一晚供氧酒店 ¥300-400
- **携程城市表需补拉萨**：`code LXA / id 112 / prov 30`；西宁用 IATA `XNN` 直调
- 详见 `references/lhasa-flight-combos.md` 与 `references/lhasa-tour-pricing.md`

## 快速开始

```bash
export PATH="/home/user/.local/bin:$PATH"
# 单程搜价
python3 compare.py search --from 武汉 --to 上海 --date 2026-10-10
# 低价日历(最多30天)
python3 compare.py calendar --from 武汉 --to 拉萨 --days 30
# 目标价监控
python3 compare.py monitor --from 武汉 --to 拉萨 --date 2026-10-01 --target 800
```

## License

MIT
