# Pmax 诊断系统完整升级 - 工作总结

## 📋 项目概述

**目标:** 将 Pmax 智能诊断系统从基础版本升级为完整业务逻辑版本

**参考文档:** [业务调优思路.md](file:///Users/mac/AI%20code/Google%20Pmax调优指导/业务调优思路.md)

**修改文件:** [pmax-analyzer.html](file:///Users/mac/AI%20code/Google%20Pmax调优指导/pmax-analyzer.html)

---

## ✅ Phase 1: 核心诊断场景 (P0/CRITICAL)

### 基建诊断增强
- **Policy Status 检查增强**: 新增消耗骤降检测,当消耗下降>40%时自动升级到 CRITICAL 级别
- **GMC Status 检查**: 保持现有逻辑

### 学习期关键场景
- **中层交互异常**: 检测 CPATC/CPIC 超标或低交互量,提供技术排查、版位清洗、受众校准建议
- **深层交互异常**: 检测高意向(ATC+IC>5)但连续零转化,提供支付测试、促销一致性检查建议

### 稳定期关键场景
- **临门一脚 (Good Traffic, Bad Offer)**: 检测加购成本正常但结账成本高的情况
- **CPM 下降诊断**: 检测 CPM 下降>20% + 展示量上升>20% + 零转化的流量降级风险

### ROI 关键场景
- **学习期零转化风险**: 增强漏斗倒推逻辑,根据 IC 有无提供不同建议
- **学习期转化成本极高**: 检测 CPA>3×Target 的熔断场景
- **稳定期零转化 (Dead Campaign)**: 检测连续2天零转化的模型死锁情况

---

## ✅ Phase 2: 高价值场景 (HIGH)

### 稳定期消耗优化
- **良性抢量**: 超预算但 CVR 稳定且 CPA 达标,建议不干预或加预算
- **高效率消耗不足**: ROAS>5.0 或 CPIC<基准,建议加预算或降低 tROAS
- **低效能消耗不足**: ROAS<4.0 且 CPIC>基准,建议检查素材和价格

### CPM 深度分析
- **CPM 上涨 + CVR 上涨**: 良性/品牌词吸血,建议检查品牌词占比
- **CPM 上涨 + CVR 下降**: 竞争加剧,建议检查 Auction Insights

### ROI 精细化
- **配件陷阱增强**: 详细的策略修正、物理隔离、信号提纯、否定词干预建议
- **CPA 正常但 AOV 过低**: 检测机器推低价配件凑单量的情况

---

## 🔧 技术实现

### 新增辅助函数
```javascript
// 环比变化计算
function calculateChange(current, previous)

// 趋势检测
function detectTrend(values, threshold)
```

### 数据结构优化
```javascript
// 新增历史对比字段
prevCost, prevIc, 
dayBeforeYesterdayCpm, dayBeforeYesterdayImpressions

// 新增环比变化计算
cpmChange, costChange, cvrChange
```

---

## 📊 诊断场景统计

| 模块 | 原有 | 新增/增强 | 合计 |
|------|------|----------|------|
| 基建诊断 | 2 | +1 增强 | 2 |
| 学习期诊断 | 6 | +2 | 8 |
| 稳定期诊断 | 4 | +6 | 10 |
| ROI 诊断 | 4 | +4 | 8 |
| **总计** | **16** | **+13** | **28** |

---

## 🎯 待完成项目

### Phase 3: 优化建议场景
- 落地页追踪异常 (需要 Sessions 数据)
- 转化延迟异常 (需要历史对比)
- 性能衰退诊断 (需要多日 ROAS 趋势数据)

---

## 📝 验证状态

- [x] 代码语法检查通过
- [x] 浏览器功能测试通过
- [ ] 真实数据验证 (待用户测试)

### 测试结果

![诊断结果截图](/Users/mac/.gemini/antigravity/brain/fd780c5a-57f4-432c-80e0-0240adc72574/diagnostic_results_1768464187498.png)

**测试数据:** 演示数据 (7天汇总)

**指标显示:**
- ROAS: 4.51 ✅
- CPA: $495 ✅
- Cost: $990 ✅
- Conv: 2 ✅

**触发场景:** 恶性暴冲 (稳定期-预算模块)

---

**完成时间:** 2026-01-15  
**版本:** v2.0
