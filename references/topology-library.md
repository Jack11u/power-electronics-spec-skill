# 拓扑库 / Topology Library

> 六大拓扑族 + 关键器件 + 电压电流应力 + 适用边界。绘图时配合 `diagram-guide.md`。
> 所有应力公式为**工程估算**，用于量级校验与选型初筛，最终以仿真/样机为准。
> 符号：Vin 输入电压，Vo 输出电压，D 占空比，n 匝比(Np:Ns)，Io 输出电流，fs 开关频率。

---

## 1. DC-DC 变换器 / DC-DC Converters

### 1.1 Buck（降压，非隔离）
- 关系：Vo = D·Vin（CCM）。功率流单向（同步 Buck 可双向）。
- 器件应力：开关管/二极管耐压 ≥ Vin；电感电流 = Io；输出电容 ESR 决定纹波。
- 适用：母线降压、点负载 POL、非隔离 48V→12V（两轮车常用同步 Buck）。
- 框图：Vin →[高边开关 Q1]→ 节点SW →[电感 L]→ Vo；[续流管/二极管 Q2/D]接 SW 到 GND；Co 并 Vo。

### 1.2 Boost（升压，非隔离）
- 关系：Vo = Vin/(1−D)。器件耐压 ≥ Vo。
- 适用：PFC 前级、光伏 MPPT 升压、电池升压母线。
- 注意：右半平面零点（RHPZ），带宽受限；D→1 时电流应力剧增。

### 1.3 Buck-Boost / 四开关 Buck-Boost
- 关系（反相 BB）：Vo = −Vin·D/(1−D)；**四开关同步 BB** 可正输出且升降压双向，两轮车/储能常用。
- 适用：宽输入范围（电池 SOC 变化大）、Vin 跨越 Vo。

### 1.4 Flyback（反激，隔离）
- 关系：Vo = Vin·(D/(1−D))·(Ns/Np)。储能于变压器气隙。
- 开关管应力：Vds ≥ Vin + n·Vo + 尖峰（需 RCD/有源钳位吸收）。
- 适用：≤150W 辅助电源、隔离偏置、OBC 的辅源、两轮充电器小功率。
- 关键器件：变压器（带气隙）、钳位吸收、光耦/数字隔离反馈。

### 1.5 Forward（正激，隔离）
- 单端正激需**磁复位**（第三绕组/RCD/有源钳位）；Vo = Vin·D·(Ns/Np)。
- 适用：100~500W 中功率隔离。

### 1.6 Full-Bridge（全桥，隔离）+ 移相
- 四开关 H 桥 + 变压器 + 副边整流；移相全桥（PSFB）实现 ZVS。
- Vo = 2·n·Vin·Deff（中心抽头/全桥整流不同系数）。
- 适用：1~5kW 隔离 DC-DC，如 OBC 后级、储能 DC-DC。

### 1.7 DAB（Dual Active Bridge，双有源桥）
- 原副边各一 H 桥 + 高频变压器 + 漏感 Llk；**天生双向**。
- 单移相功率：P = (n·V1·V2)/(2·fs·L) · φ(1−|φ|/π)，φ 为移相角。
- 应力：开关电流随移相角/电压比变化；轻载回流功率大 → 用 TPS 优化。
- 适用：V2G、储能双向 DC-DC、多合一电源级间，800V/400V 互联。
- 进阶控制见 control-strategies.md 的 **TPS-DAB**。

### 1.8 LLC / CLLLC 谐振
- **LLC**：Lr-Lm-Cr 谐振，副边整流。fs≈fr 时全负载 ZVS/ZCS，效率高。单向。
- **CLLLC**：原副边对称谐振网络（Cr-Lr 两侧），**双向**，OBC 双向/储能首选。
- 增益随 fs 调节（调频控制）；轻载增益控制难 → 加 burst/PWM 混合。
- 适用：高效隔离 DC-DC，OBC 后级 6.6/11/22kW、800V 平台。

**DC-DC 选型速查**：
| 功率 | 隔离 | 双向 | 推荐 |
|---|---|---|---|
| <150W | 是 | 否 | Flyback（有源钳位） |
| 0.1~0.5kW | 是 | 否 | Forward / 半桥 |
| 1~5kW | 是 | 否 | PSFB / LLC |
| 3~22kW | 是 | 是 | CLLLC / DAB |
| 任意 | 否 | 可 | (四开关)Buck-Boost / 同步 Buck |

---

## 2. 逆变器 / Inverters

### 2.1 单相/三相两电平
- 三相两电平 6 管桥（IGBT/SiC MOSFET），输出线电压 √3·Vdc/2·m（SVPWM，m 调制比）。
- 器件耐压 ≥ Vdc（母线），留 ≥50% 裕量（母线尖峰）。
- 适用：车用 MCU 驱动 PMSM、电动摩托车驱动、储能 PCS 并网。

### 2.2 多电平（NPC / T-Type / MMC）
- **三电平 NPC**：每相 4 管 + 2 钳位二极管；开关应力 = Vdc/2，输出谐波小，适合 800V/1500V。
- **T-Type**：中点用双向开关，低压侧损耗更低。
- **MMC**：子模块级联，适合中高压/HVDC，见 control-strategies.md 的 **MMC-HVDC**。

### 2.3 调制
- **SPWM**：正弦调制，直观，母线利用率 = 0.5·Vdc（相电压幅值）。
- **SVPWM**：空间矢量，母线利用率提升 15.5%（0.577·Vdc），车用电驱标配。
- **DPWM**：不连续调制，降开关损耗（每周期钳位 1/3），适合高频/高效。

---

## 3. 整流器 / Rectifiers
- **不控**：二极管桥，简单但功率因数低、谐波大。
- **半控**：晶闸管+二极管，可调但谐波仍大。
- **全控**：全 IGBT/MOSFET PWM 整流，可四象限、单位功率因数（＝有源前端 AFE）。
- **Vienna 整流器**：三相三电平单向 PFC，3 开关 + 二极管网络，输出中点，谐波低、器件少，充电桩/服务器电源常用。开关耐压 = Vo/2。

---

## 4. PFC / 功率因数校正
- **Boost PFC**：经典，二极管桥 + Boost，Vo（母线）> Vin 峰值（如 400V）。
- **Bridgeless PFC**：去掉输入整流桥，降导通损耗（两路 Boost 交替）。
- **Totem-Pole（图腾柱）PFC**：无桥，两个快管（GaN/SiC）+ 两个工频管，双向、效率 >99%。GaN 图腾柱是 OBC/充电桩前级主流。见 control-strategies.md 的 **Totem-Pole GaN PFC**。
- **Interleaved（交错并联）**：多路 Boost 相位错开，纹波抵消、电流分担，见 **Interleaved Boost PFC**。
- 母线电压：单相通常 380~420V；三相 700~800V。

---

## 5. 调制与移相汇总 / Modulation
| 方法 | 用途 | 要点 |
|---|---|---|
| SPWM | 逆变/整流 | 正弦参考比较三角载波 |
| SVPWM | 电驱/PCS | 母线利用率高，谐波低 |
| DPWM | 高效逆变 | 钳位降开关损耗 |
| PSM（移相）| 全桥/DAB | 相位差调功率，配合 ZVS |
| PFM（调频）| LLC/CLLLC | 靠频率调增益 |
| PWM+PFM 混合 | 谐振轻载 | burst 模式防失控 |

---

## 6. 混合 / 新能源接口
- **储能双向 PCS**：AC/DC（三相 AFE，SVPWM）+ DC/DC（DAB/CLLLC）两级；支持并网/离网/VSG。
- **光伏 MPPT**：Boost/Buck-Boost 前级 + MPPT 算法（扰动观察 P&O / 增量电导 INC）。
- **V2G/V2X**：OBC 具备双向能力（Totem-Pole PFC 双向 + CLLLC/DAB 双向）。
- 进阶：**STATCOM**（无功补偿）、**VSG**（虚拟同步机）见 control-strategies.md。

---

## 拓扑选择决策树 / Decision Tree
1. 需要电气隔离？ 是→变压器类（Flyback/Forward/PSFB/LLC/CLLLC/DAB）；否→Buck/Boost/BB。
2. 需要双向能量流？ 是→DAB/CLLLC/同步 Buck/四开关 BB/AFE；否→单向拓扑。
3. AC 接口？ 是→前级 PFC（Totem-Pole/Vienna/Interleaved）+ 后级隔离 DC-DC。
4. 电机驱动？ 三相逆变（两电平 SVPWM / 三电平 NPC）+ FOC。
5. 电压等级 >750V？ 优先三电平/SiC；MW 级中压→MMC。
6. 效率 >99% 诉求？ GaN 图腾柱 PFC + LLC/CLLLC，高 fs。
