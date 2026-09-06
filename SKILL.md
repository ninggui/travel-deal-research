---
name: travel-deal-research
description: 机票/酒店/火车比价与盯价，含往返组合计算与拉萨线特殊处理。触发：查机票、查酒店、盯低价、出行攻略。
slug: travel-deal-research
displayName: 出行比价盯价
version: 1.0.0
---

# 机票酒店出行比价与盯价

> 用户 2026-08-26 建立：出行前先做多平台比价+往返组合计算+盯价监控，并把"综合时间+住宿成本"纳入对比（用户原话：红眼航班要算住宿，卧铺直达白天到可直接玩）。

## 已装技能（SkillHub travel-skills 系列，同一 SCF 代理零配置）

```bash
export PATH="/home/user/.local/bin:$PATH"
# 已装在 /home/user/skills/@clawhub_travel-skills/ 下：
#   fliggy-travel       飞猪全旅行（酒店/机票/火车票/门票/美食/万豪）
#   flight-price-track  机票比价+低价日历+目标价监控（飞猪+途牛+RG）
#   hotel-price-monitor 酒店比价+降价监控（飞猪+途牛+同程+美团+RG）
#   smart-flight-buy / hotel-smart-book  决策增强版（同代理）
# 携程系：@clawhub_hi-yu/ctrip-flights（需 quickjs）、@clawhub_alexfeng75/ctrip-hotel-search（需 Playwright+MATON key，慎用）
# 另装：find-skills、summarize、ppt-generator-skill-pro
```

## 数据源可用性（2026-08-26 实测）

| 源 | 状态 | 说明 |
|----|------|------|
| 飞猪 SCF 代理 | ✅ 可用 | `compare.py search/calendar/monitor` 返回真实数据 |
| 途牛 SCF 代理 | ❌ 代理端 bug | `NameError: name 're' is not defined`（上游故障，非本机问题） |
| RG 源 | ❌ 已暂停 | 2026-07-27 起"RG机票MCP服务暂停架构升级" |
| 携程 ctrip_flight.py | ✅ 可用 | 需 `pip install quickjs`；城市表需补拉萨（见下）；`get_low_prices` 返回低价日历 |
| 飞猪对拉萨方向 | ❌ 无数据 | `search_flight` journeyType 0/1/2 均返回空 |

**关键 API 形态**：
- 机票：`python3 compare.py search --from 武汉 --to 上海 --date YYYY-MM-DD`（--json 输出）
- 低价日历：`compare.py calendar --from X --to Y --start-date D --days N`（逐日查询，最多30天）
- 目标价监控：`compare.py monitor --from X --to Y --date D --target 800`（输出结构化监控，宿主承接 cron）
- 酒店：`compare.py search --city 上海 --check-in D1 --check-out D2`
- 携程低价日历：`ctrip_flight.py 成都 拉萨 2026-09-05 --json`（lowPriceCalendar 字段，含 price + totalPrice 含税价）

## ⚠️ 拉萨等小众航线特殊处理（2026-08-26 血泪）

1. **飞猪对拉萨方向全线无数据**（成都/重庆/西安/昆明/兰州/西宁/贵阳→拉萨都是 0 flights），必须走携程源
2. **携程城市表缺拉萨**：`ctrip_flight.py` 的 `CITY_INFO` 需补：
   ```python
   "拉萨": {"code": "LXA", "id": 112, "prov": 30},
   ```
3. **西宁也不在城市表** → 直接用 IATA code 调 `get_low_prices('XNN','LXA')`（绕过 get_city）
4. **CLI 与 import 行为不同**：`search_to_region()` 不返回低价日历，日历在 `main()` 里单独调 `get_low_prices()`；批量脚本要直接调 `cf.get_low_prices(dep, arr, date)` + `cf.parse_low_prices()`

## 往返组合计算流程（用户指定打法：先罗列中转城市→再组合查价）

1. **先测可行中转**：对每个候选枢纽跑 `search` 看是否有数据（拉萨线选成都/重庆/西安/昆明/兰州/西宁/贵阳）
2. **分4段拉低价日历**：武汉→枢纽、枢纽→拉萨、拉萨→枢纽、枢纽→武汉
3. **组合计算**：去程日期 d + 回程 r（r-d ∈ [7,8] 天），每段取 `totalPrice`（含税），`去程段1+段2+回程段1+段2 = 总价`，遍历所有枢纽对，排序取最低
4. 注意直达无数据≠没票：飞猪空 → 携程有（成都→拉萨低价日历实测 ¥310-420）
5. 结果给 Top 组合表 + 日期/中转城市/分段价，附"下单前复核实时价"提醒

## 防限流节奏（用户明确要求：模拟人工搜索速度）

- **批量间隔 2-5s 随机延时**（不要 0.3-0.8s 机器节奏，携程有 c-sign 签名+UA 指纹反爬）
- 失败自动退避：间隔翻倍重试
- 单批 ≤8 段查询，分轮跑
- 低频查询（≤5 次）可快，连续 10+ 次必须加延时

## 综合时间+住宿成本对比（用户要求纳入）

- 红眼航班：凌晨到 → 必须多住 1 晚（拉萨供氧酒店 ¥300-400）→ 实际比白天航班贵且损失白天
- 卧铺直达：如武昌→拉萨 Z264 硬卧 ¥741 / 41h56m，睡车上省 2 晚住宿，白天看青藏线风景，逐步适应高反
- 对比表维度：交通费 / 途中时间 / 住宿成本 / 到达状态 / 综合成本（交通+住宿）
- 火车方案参考：Z264 武昌→拉萨 41h56m 硬卧741 软卧1177；G852 武汉→西宁高铁 8h47m 二等843；Z6811 西宁→拉萨 22h06m；G852 到西宁 20:32 赶 21:50 的 Z6811 仅 78 分钟太险，直达卧铺更省事
- 攻略要点：首次进藏火车优先（供氧列车+逐步适应）；飞机选上午抵达；布达拉宫门票提前 7-10 天实名抢

## 拉萨旅游团调研结论（2026-08-26，详见 references/lhasa-tour-pricing.md）

- **线上 vs 线下价差属实**：线上中间价 ¥2500（7天6晚含住宿/小团8人/门票）≈ 渠道价；线下地接门市拼团 ¥1600-1800 = 不含大交通的落地价（官方门市散拼：珠峰4日¥1350、林芝3日¥1250、日喀则2日¥660）
- **危险线**：低于 ¥999-1500 的"7日纯玩"= 购物团（多个来源一致警告）
- **门票硬成本**：布宫200+大昭寺85+纳木错120+羊湖60 = ¥465，报价低于"价格-465"必有猫腻
- 7天6晚黄金路线：D1适应 → D2布宫+大昭寺 → D3羊湖 → D4纳木错 → D5林芝过渡 → D6雅鲁藏布大峡谷 → D7返程

## 用户调研偏好（多平台验证）

- **剔除广告软文**：搜索结果中带电话号码/微信/"顾问XX老师"/"旅行社排行榜 TOP1" 的推荐 = 广告软文，一律剔除
- 可信来源排序：官方门市官网（明码标价+已购人数）> 新浪/旅游官网实测文章 > 用户实测分享（小红书/马蜂窝，交叉出现多次）> 软文
- 多平台交叉验证：同一价格说法至少要 2 个独立来源确认
- 输出剔除来源标注（✅官网/✅用户实测），明确判断依据

## 支持文件
- `references/lhasa-tour-pricing.md` — 拉萨旅游团线上/线下价差验证+路线+避坑明细
- `references/lhasa-flight-combos.md` — 武汉↔拉萨往返中转组合实测数据（2026-08-26）