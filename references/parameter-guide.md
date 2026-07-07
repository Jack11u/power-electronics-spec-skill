# 参数选型与量级校验指南 / Parameter Selection & Sanity Check Guide

> 工程估算公式，用于初步选型与自洽性检查。最终以仿真/实测为准。

---

## 基础功率关系 / Basic Power Relations

### 功率守恒
- **输入功率 ≈ 输出功率 / 效率**：Pin = Po / η
- **电压电流关系**：P = V × I（DC），P = √3 × VL × IL × cosφ（三相 AC）
- **检查点**：输入电流 Iin = Po / (Vin_min × η)，输出电流 Io = Po / Vo

**示例**：6.6kW OBC，Vin = AC 220V，η = 95%
- Pin = 6600 / 0.95 ≈ 6950W
- Iin_rms ≈ 6950 / 220 ≈ 31.6A（需考虑功率因数，实际 ≈32~35A）

---

## 器件应力估算 / Component Stress Estimation

### 开关管 MOSFET / IGBT
- **电压应力 Vds/Vce**：
  - Buck：Vds ≥ Vin_max（留 20~50% 裕量）
  - Boost：Vds ≥ Vo_max
  - 全桥/半桥：Vds ≥ Vbus
  - Flyback：Vds ≥ Vin + n·Vo + Vspike（尖峰 ≈20~50V，用钳位抑制）
- **电流应力 Id**：
  - 连续模式 CCM：Id_rms ≈ Io_avg / √D（Buck 高边）、Io_avg / √(1−D)（Boost）
  - 峰值电流：Ipk ≈ Io + ΔiL/2
- **导通损耗**：Pcond = Id_rms² × Rds(on) × D（占空比 D 时间段导通）
- **开关损耗**：Psw ≈ 0.5 × Vds × Id × (tr+tf) × fs，其中 tr/tf 为上升/下降时间
- **总损耗**：Ptotal = Pcond + Psw + Qg_loss，Qg_loss = Qg × Vgs × fs（栅极驱动损耗）

### 二极管
- **电压**：反向耐压 ≥ 正向最大电压 + 裕量（如 Buck 续流二极管 ≥ Vin）
- **电流**：平均 If_avg ≈ Io × (1−D)（Buck 续流），峰值 Ifm ≥ Ipk
- **反向恢复**：快恢复二极管 trr <50ns（硬开关），肖特基 SBD 无 trr（≤100V 优选）

### 磁件（电感/变压器）
- **电感量 L**：
  - Buck/Boost：L = (Vin−Vo) × D / (ΔiL × fs)（Buck），ΔiL 纹波电流取 Io 的 20~40%
  - Flyback 初级电感 Lm：Lm = Vin² × D² / (2 × Po × fs)
- **峰值电流**：Ipk = Io + ΔiL/2（不饱和），磁芯气隙调整防饱和
- **磁芯损耗**：Pcore ∝ Bm^α × fs^β（α≈2.5，β≈1.5，Steinmetz 方程），Bm = V × ton / (N × Ae)
- **绕组损耗**：Pcu = I_rms² × Rac，Rac = Rdc × (1 + k_skin + k_prox)，高频趋肤/邻近效应

### 电容
- **母线电容**：Cbus = Po × Δt / (Vbus × ΔVbus)，Δt 为半周期时间（如单相 10ms@50Hz），ΔVbus 为允许纹波（通常 5~10%）
  - 示例：6.6kW OBC，Vbus=400V，ΔV=20V，Δt=10ms → C = 6600×0.01 / (400×20) ≈ 8250μF（实际配 2~3 倍余量，如 20mF）
- **输出电容**：Co ≥ ΔiL / (8 × fs × ΔVo)，ΔVo 为输出纹波
- **ESR 纹波**：Vripple_ESR = ΔiL × ESR，低 ESR 电容（聚合物/MLCC 并联电解）
- **电压裕量**：耐压 ≥ 1.5 × 工作电压（电解电容寿命与电压应力指数相关）

---

## 效率链核算 / Efficiency Chain

从输入到输出，每级效率相乘：
- **两级变换**（如 PFC + DC-DC）：η_total = η_PFC × η_DCDC
  - 示例：PFC 97%，CLLLC 98% → 总效率 = 0.97×0.98 = 95.06%
- **三级**（如 PFC + 隔离 + 后稳压）：η_total = η1 × η2 × η3
- **检查点**：输入功率 Pin = Po / η_total；各级损耗热设计合理（散热器 Rth、风扇）

---

## 开关频率选择 / Switching Frequency

| fs 范围 | 适用 | 优势 | 代价 |
|---|---|---|---|
| 20~50kHz | IGBT 硬开关，大功率 | 开关损耗可控 | 磁件大，滤波重 |
| 50~150kHz | Si MOSFET，通用 | 平衡性能与体积 | 标准范围 |
| 100~500kHz | GaN/SiC，高效小型化 | 磁件/电容小，功率密度高 | EMI 难度大，驱动快 |
| >500kHz | GaN 极致，无线电源 | 超小体积 | EMI/layout 敏感 |

---

## 热设计速算 / Thermal Quick Estimate

- **结温 Tj**：Tj = Ta + Ploss × (Rth_jc + Rth_cs + Rth_sa)
  - Rth_jc：结到壳（器件数据手册）
  - Rth_cs：壳到散热器（导热垫/硅脂，≈0.5~2 ℃/W）
  - Rth_sa：散热器到环境（散热器选型，自然对流 ≈10~20 ℃/W，强制风冷 ≈2~5 ℃/W）
- **检查点**：Tj < Tj_max − 20℃（留余量），IGBT Tj_max=150℃、SiC=175℃、GaN=150℃
- **示例**：器件损耗 Ploss=30W，Ta=85℃，Rth_jc=0.5，Rth_cs=1，Rth_sa=3 → Tj = 85+30×(0.5+1+3) = 220℃（超温！需更大散热器或风扇）

---

## 绝缘与安全裕量 / Insulation & Safety Margin

### 爬电距离与电气间隙（GB/T 18384、IEC 60664）
| 工作电压 Vdc | 污染等级 2 | 污染等级 3 |
|---|---|---|
| ≤150V | 1.5mm | 2.5mm |
| 400V | 8mm | 12mm |
| 800V | 16mm | 24mm |

- **爬电距离**：沿绝缘表面测量的最短路径。
- **电气间隙**：空气中直线最短距离。
- **裕量**：实际布局 ≥1.2 倍标准值。

### 绝缘电阻
- **要求**：≥100Ω/V（如 400V 系统 ≥40MΩ），ISO 6469、GB/T 18384。
- **测试**：500V DC 测试电压，1 分钟。

### 耐压测试
- **AC**：2 × Un + 1000V（1 分钟），如 400V 系统 → 1800V AC。
- **DC**：1.5 × (2Un + 1000V)。
- **无击穿、无闪络。**

---

## 量级自洽检查表 / Sanity Checklist

执行以下检查，任一不通过需回溯修正：

- [ ] **功率平衡**：Pin = Po/η，各级输入输出功率匹配。
- [ ] **电压应力**：开关管耐压 ≥ 1.2~1.5 倍最大工作电压。
- [ ] **电流应力**：开关管 Id_rated ≥ 1.5 倍 Ipk，二极管 If_avg ≥ Io。
- [ ] **磁件不饱和**：Ipk < Isat（磁芯饱和电流），气隙设计合理。
- [ ] **电容纹波**：母线电容容量足够，ESR 满足纹波要求。
- [ ] **效率可达**：各级效率乘积 = 目标总效率，损耗热设计可行。
- [ ] **开关频率可行**：fs 匹配器件能力（IGBT <50kHz，SiC >100kHz，GaN >200kHz）。
- [ ] **热设计余量**：Tj < Tj_max − 20℃。
- [ ] **爬电/电气间隙**：高压布局满足标准。
- [ ] **绝缘电阻**：≥100Ω/V，耐压测试电压计算正确。

---

## 常见取值参考 / Typical Values Reference

| 参数 | 两轮车/小功率 | 乘用车 OBC/MCU | 储能/快充 |
|---|---|---|---|
| 输入电压 | 48V / AC 220V | AC 220V / DC 250~450V | AC 380V / DC 200~1000V |
| 输出电压 | 12V / 60V | DC 250~450V / 三相 AC | DC 200~800V |
| 功率 | 0.5~3kW | 6.6~22kW | 30~350kW |
| 开关频率 | 50~100kHz | 100~200kHz | 20~100kHz |
| 效率目标 | >90% | >95% | >97% |
| 母线电容 | 数百 μF | 数千~万 μF | 数万 μF / 薄膜 |
| 冷却 | 自然对流 / 小风扇 | 强制风冷 | 液冷 / 强制风冷 |

---

## 附录：常用公式速查 / Formula Quick Reference

- **Buck Vo** = D·Vin
- **Boost Vo** = Vin/(1−D)
- **Flyback Vo** = Vin·(D/(1−D))·(Ns/Np)
- **全桥 Vo** = 2·n·Vin·Deff（中心抽头整流）
- **LLC 增益 M** = Vo/(n·Vin)，调频控制
- **三相逆变线电压** = √3·(Vdc/2)·m（SVPWM）
- **PFC 母线** Vo = 1.35·Vrms（单相），≈1.35×220 = 297V（实际设 380~420V）
- **电感 ΔiL** = V·Δt/L，Δt = D/fs（导通时间）
- **电容充电时间常数** τ = R·C
