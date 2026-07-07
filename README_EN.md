# Power Electronics Component Spec Generator

**电力电子功率零部件技术文件生成技能**

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Claude Skill](https://img.shields.io/badge/Claude-Skill-purple.svg)](https://claude.ai)

> 🌏 [中文文档 / Chinese README](README.md)

## 📋 Overview

A Claude skill designed for **new-energy power electronics engineers**. From a one-sentence requirement, it automatically generates complete technical documentation for power electronic components, including:

- ✅ **Statement of Requirements (SOR)**
- ✅ **Technical Solution Document** (supplier design proposal)
- ✅ **Product Specification (Spec)**
- ✅ **Diagram Set** (topology diagram, HV electrical schematic, circuit schematic)

### 🎯 Target Products

- **New-Energy Vehicles (NEV)**: On-Board Charger (OBC), DC-DC converter, HV junction box (PDU), Motor Control Unit (MCU), DC fast-charging module
- **E-motorcycles / E-bikes**: Chargers, controllers, DC-DC, BMS front-end
- **General New Energy**: Energy-storage PCS, PV MPPT, V2G bidirectional converters, STATCOM

### 🔥 Core Capabilities

1. **Six Topology Families**: DC-DC (Buck/Boost/Flyback/DAB/LLC/CLLLC), inverters, rectifiers, PFC, modulation strategies, storage & PV
2. **10 Advanced Control Models**: Totem-Pole GaN PFC, MMC HVDC, PMSM FOC, VSG, CLLLC, FCS-MPC, ADRC, TPS-DAB, Interleaved Boost PFC, STATCOM
3. **Engineering-Grade Accuracy**: Complete HV safety elements (contactors / pre-charge / fuse / MSD / IMD / HVIL), topology-connection consistency checks, component stress sanity checks
4. **Automotive-Grade Templates**: Merged from three production automotive SOR templates (FDC215, E50MCE, S508)

---

## 🚀 Quick Start

### 1. Install the Skill

Copy the `power-electronics-spec` folder into your Claude skills directory:

```bash
# Path depends on your Claude configuration
cp -r power-electronics-spec ~/.claude/skills/
```

Or import the skill package (.skill file) via Claude Desktop.

### 2. Trigger the Skill

In a Claude conversation, use any of these triggers:

```
"Create an SOR for a 6.6kW OBC"
"Generate a technical solution for an 800V HV junction box"
"I need a spec sheet for an e-motorcycle charger"
"/pe-spec"
```

### 3. Example Conversation

**User**:
> I need an SOR for a 6.6kW unidirectional OBC for an EV. Input AC 220V, output 250–450V charging the traction battery.

**Claude** (after invoking this skill):
- Asks for key requirements (efficiency target, volume constraints, cooling method, etc.)
- Generates the solution-background section
- Selects topology (Totem-Pole GaN PFC + CLLLC isolated DC-DC) and draws the topology diagram
- Builds the parameter table and runs sanity checks
- Outputs a complete SOR document (with HV schematic and circuit schematic)
- Optional: exports a Word document (.docx)

---

## 📚 Directory Structure

```
power-electronics-spec/
├── SKILL.md                    # Main skill file (loaded by Claude)
├── README.md                   # Chinese README
├── README_EN.md                # English README (this file)
├── LICENSE                     # MIT License
├── references/                 # Reference library
│   ├── sor-template.md         # SOR template
│   ├── solution-template.md    # Technical solution template
│   ├── spec-template.md        # Specification template
│   ├── topology-library.md     # Topology library (six families)
│   ├── control-strategies.md   # Control strategies (10 advanced models)
│   ├── parameter-guide.md      # Parameter selection & sanity checks
│   ├── diagram-guide.md        # Diagram drawing guide (highest priority)
│   └── standards.md            # Applicable standards list
├── scripts/
│   └── gen_docx.py             # Markdown → Word exporter (optional)
├── examples/
│   └── example-obc-6kw6-sor.md # 6.6kW OBC complete example
└── assets/
    └── (reserved for images, etc.)
```

---

## 🔬 Technical Features

### Diagram Accuracy Rules

The core value of this skill is **engineering-grade accurate diagrams**. Three diagram types, each with a distinct role:

1. **Topology Diagram**: power flow, conversion stages, bus voltages, isolation boundaries (Mermaid-rendered)
2. **HV Electrical Schematic**: HV+/HV−, main contactors, pre-charge circuit, fuse, MSD, insulation monitoring, HVIL, equipotential bonding (9 mandatory safety elements)
3. **Circuit Schematic**: actual devices, gate drivers, sensing, magnetics polarity, snubbers (ASCII + component table)

**HV safety elements are mandatory**: K1/K2 main contactors, R_pre+K_pre pre-charge, fuse F, Manual Service Disconnect (MSD), Insulation Monitoring Device (IMD), High-Voltage Interlock (HVIL), protective earth (PE), Y-capacitors, creepage-distance annotations.

**Topology self-consistency**: no shoot-through in half-bridge legs, transformer dot-polarity marked, rectifier diode orientation correct, capacitor polarity correct.

### Sanity-Check Formulas

Before delivering any document, it automatically runs:
- Power balance: Pin = Po/η
- Device voltage stress: ≥1.2–1.5× max operating voltage
- Current stress: switch Id ≥1.5× Ipk
- Magnetics non-saturation: Ipk < Isat
- Efficiency chain: product of stage efficiencies
- Thermal design: Tj < Tj_max − 20°C

---

## 🛠️ Use Cases

| Scenario | Deliverable | Typical Use |
|---|---|---|
| **Buyer issues requirements** | SOR | OEM tenders to suppliers, defines dev requirements |
| **Supplier bids/designs** | Technical Solution | Supplier responds to SOR, justifies implementation |
| **Product marketing** | Specification | Product brochures, selection guides, datasheets |
| **Engineering review/debug** | Diagram Set | Electrical design review, PCB design basis, debug reference |

---

## 📖 Sample Output

See `examples/example-obc-6kw6-sor.md` (6.6kW OBC SOR) for a complete example.

**The generated document includes**:
- Revision history, overview/engineering summary
- General description (background, operating modes, key design conclusions)
- Electrical schematics (all three diagram types, Mermaid-renderable)
- Basic parameter tables (sanity checks passed)
- Signal / interface definition tables
- Test item list
- Restricted substances & deliverables requirements
- Appendices (interface definitions, standards list, DV test details)

---

## ⚙️ Advanced Features

### Optional: Markdown → Word Export

```bash
python scripts/gen_docx.py input.md output.docx
```

Requires: `pip install python-docx markdown`

### Custom Topology Library

Edit `references/topology-library.md` to add proprietary topologies (e.g., multilevel converters, special resonant topologies).

### Control Strategy Extensions

Edit `references/control-strategies.md` to add in-house control algorithms (e.g., proprietary MPC variants).

---

## 🤝 Contributing

Issues and Pull Requests are welcome!

**Contribution ideas**:
- New topologies (Quasi-Z-Source, Matrix Converter, etc.)
- More standards (UL, EN, UN ECE details)
- More examples (MCU, HV junction box, two-wheeler chargers)
- Translation & localization

---

## 📜 License

MIT License © 2026

---

## 🙏 Acknowledgements

- TOC structures referenced from three production automotive SORs (FDC215, E50MCE, S508)
- Topology library and control strategies distilled from power-electronics textbooks and engineering practice
- Powered by Anthropic Claude

---

**Let AI be your power-electronics co-engineer 🚗⚡**
