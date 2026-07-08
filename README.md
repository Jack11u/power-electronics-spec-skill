# 电力电子功率零部件技术文件生成技能

**Power Electronics Component Spec Generator**

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Claude Skill](https://img.shields.io/badge/Claude-Skill-purple.svg)](https://claude.ai)

## 📋 简介

这是一个专为 **新能源电力电子工程师** 设计的 Claude 技能，能够基于一句话需求，自动生成完整的功率电子零部件技术文档，包括：

- ✅ **技术要求书 SOR**（Statement of Requirements）
- ✅ **技术方案说明书**（乙方设计方案）
- ✅ **产品规格书 Spec**（参数规格表）
- ✅ **图纸集**（电气拓扑图、高压电气原理图、电路原理图）

### 🎯 适用产品

- **新能源汽车**：OBC 车载充电机、DC-DC 变换器、高压盒 PDU、MCU 电机控制器、直流快充模块
- **电动摩托车/自行车**：充电器、控制器、DC-DC、BMS 前端
- **通用新能源**：储能 PCS、光伏 MPPT、V2G 双向变换、STATCOM

### 🔥 核心能力

1. **六大拓扑族电路库**：DC-DC（Buck/Boost/Flyback/DAB/LLC/CLLLC）、逆变器、整流器、PFC、调制策略、储能光伏
2. **10 个高级控制模型**：Totem-Pole GaN PFC、MMC HVDC、PMSM FOC、VSG、CLLLC、FCS-MPC、ADRC、TPS-DAB、Interleaved Boost PFC、STATCOM
3. **工程级准确性保证**：高压安全元素完整（接触器/预充/熔断/MSD/IMD/HVIL）、拓扑连接自洽性校验、器件应力量级核算
4. **车规级文档模板**：融合三份量产车规 SOR 模板（FDC215、E50MCE、S508）

---

## 🚀 快速开始

### 1. 安装技能

将 `power-electronics-spec` 文件夹复制到你的 Claude 技能目录：

```bash
# 具体路径依 Claude 配置而定
cp -r power-electronics-spec ~/.claude/skills/
```

或通过 Claude Desktop 导入技能包（.skill 文件）。

### 2. 触发技能

在 Claude 对话中，使用以下任意触发词：

```
"帮我做一个 6.6kW OBC 的技术要求书"
"生成 800V 高压盒的技术方案"
"需要一份电动摩托车充电器规格书"
"/pe-spec"
```

### 3. 示例对话

**用户**：
> 我需要一个新能源汽车用的 6.6kW 单向 OBC（车载充电机）技术要求书，输入 AC 220V，输出 250~450V 给动力电池充电。

**Claude**（调用本技能后）：
- 询问关键需求（效率目标、体积约束、冷却方式等）
- 生成方案背景章节
- 选择拓扑（Totem-Pole GaN PFC + CLLLC 隔离 DC-DC）并绘制拓扑图
- 建立参数表并校验量级
- 输出完整 SOR 文档（含高压电气原理图、电路原理图）
- 可选：生成 Word 文档（.docx）

---

## 📚 文档结构

```
power-electronics-spec/
├── SKILL.md                    # 技能主文件（Claude 加载）
├── README.md                   # 中文说明文档（本文件）
├── README_EN.md                # 英文说明文档
├── LICENSE                     # MIT 许可证
├── references/                 # 参考文件库
│   ├── sor-template.md         # 技术要求书模板
│   ├── solution-template.md    # 技术方案说明书模板
│   ├── spec-template.md        # 规格书模板
│   ├── topology-library.md     # 拓扑库（六大族）
│   ├── control-strategies.md   # 控制策略库（10 个高级模型）
│   ├── parameter-guide.md      # 参数选型与量级校验
│   ├── diagram-guide.md        # 图纸绘制规范（最高优先级）
│   └── standards.md            # 适用标准清单
├── scripts/
│   └── gen_docx.py             # Markdown → Word 导出脚本（可选）
├── examples/
│   └── example-obc-6kw6-sor.md # 6.6kW OBC 完整示例
└── assets/
    └── (预留，存放图片等)
```

---

## 🔬 技术特性

### 图纸准确性铁律

本技能最核心的价值是**工程级准确的图纸**。三类图各司其职：

1. **电气拓扑图**：功率流、变换级、母线电压、隔离边界（Mermaid 渲染）
2. **高压电气原理图**：HV+/HV−、主接触器、预充回路、熔断器、MSD、绝缘监测、HVIL、等电位（必含 9 大安全元素）
3. **电路原理图**：具体器件、驱动、采样、磁件极性、缓冲吸收（ASCII + 器件表）

**高压安全元素不可省略**：K1/K2 主接触器、R_pre+K_pre 预充、熔断器 F、手动维修开关 MSD、绝缘监测 IMD、高压互锁 HVIL、保护接地 PE、Y 电容、爬电距离标注。

**拓扑连接自洽**：开关桥臂上下管不直通、变压器同名端标注、整流二极管方向正确、电容极性正确。

### 量级校验公式

每份文档交付前，自动执行：
- 功率平衡：Pin = Po/η
- 器件电压应力：≥1.2~1.5 倍最大工作电压
- 电流应力：开关管 Id ≥1.5×Ipk
- 磁件不饱和：Ipk < Isat
- 效率链核算：各级效率乘积
- 热设计：Tj < Tj_max − 20℃

---

## 🛠️ 使用场景

| 场景 | 产出 | 典型用例 |
|---|---|---|
| **甲方下需求** | SOR 技术要求书 | 主机厂向供应商招标，定义开发要求 |
| **乙方投标/设计** | 技术方案说明书 | 供应商回应 SOR，论证如何实现 |
| **产品对外宣传** | 规格书 Spec | 产品手册、选型、数据手册 |
| **工程评审/调试** | 图纸集 | 电气设计评审、PCB 设计依据、调试参考 |

---

## 📖 示例产出

完整示例见 `examples/example-obc-6kw6-sor.md`（6.6kW OBC 技术要求书）。

**生成的文档包含**：
- 更改记录、说明/工程概要
- 总体概述（方案背景、工作模式、关键设计结论）
- 电气原理图（三类图齐全，Mermaid 可直接渲染）
- 基本参数要求表（量级校验通过）
- 信号定义/接口定义表
- 测试项目表
- 禁用物质 & 交付资料要求
- 附件（接口定义、标准清单、DV 试验明细）

---

## ⚙️ 高级功能

### 可选：Markdown → Word 导出

```bash
python scripts/gen_docx.py input.md output.docx
```

需安装依赖：`pip install python-docx markdown`

### 自定义拓扑库

编辑 `references/topology-library.md`，添加你的专有拓扑（如多电平变换、特殊谐振拓扑）。

### 控制策略扩展

编辑 `references/control-strategies.md`，加入企业内部控制算法（如自研 MPC 变体）。

---

## 🤝 贡献

欢迎提交 Issue 和 Pull Request！

**贡献方向**：
- 新增拓扑（如 Quasi-Z-Source、Matrix Converter）
- 更多标准（如 UL、EN、UN ECE 细则）
- 更多示例（MCU、高压盒、两轮充电器）
- 翻译与本地化

---

## 📜 许可证

MIT License © 2026

---

## 🙏 致谢

- 参考了多份量产车规 SOR的目录结构
- 拓扑库与控制策略总结自《电力电子技术》《新能源汽车电力电子系统》等教材与工程实践

---

## 📞 联系

- GitHub Issues: [https://github.com/your-repo/power-electronics-spec/issues](https://github.com/your-repo/power-electronics-spec/issues)
- 讨论/交流：欢迎在 Issues 中分享你的使用案例

