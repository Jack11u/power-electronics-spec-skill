# Power Electronics Spec Generator - Installation & Usage Guide

## 📦 Package Contents

This skill package (`power-electronics-spec-skill.zip`) contains:

```
power-electronics-spec/
├── SKILL.md                    # Main skill file (loaded by Claude)
├── README.md                   # Chinese documentation
├── README_EN.md                # English documentation
├── LICENSE                     # MIT License
├── .gitignore                  # Git ignore rules
├── references/                 # Reference library (8 files)
│   ├── sor-template.md         # SOR template (merged from 3 automotive SORs)
│   ├── solution-template.md    # Technical solution template
│   ├── spec-template.md        # Product specification template
│   ├── topology-library.md     # 6 topology families + device stress formulas
│   ├── control-strategies.md   # 10 advanced control models
│   ├── parameter-guide.md      # Parameter selection & sanity checks
│   ├── diagram-guide.md        # Diagram drawing rules (highest priority)
│   └── standards.md            # Applicable standards list
├── scripts/
│   └── gen_docx.py             # Markdown → Word export script
└── examples/
    └── example-obc-6kw6-sor.md # Complete 6.6kW OBC SOR example
```

Total size: ~108 KB (20 files)

## 🚀 Installation

### Method 1: Direct folder installation
```bash
# Extract the zip
unzip power-electronics-spec-skill.zip

# Copy to Claude skills directory (path varies by system)
cp -r power-electronics-spec ~/.claude/skills/

# Or for Claude Desktop:
cp -r power-electronics-spec ~/Library/Application\ Support/Claude/skills/
```

### Method 2: Claude Desktop import
1. Open Claude Desktop
2. Settings → Skills → Import
3. Select `power-electronics-spec-skill.zip`

## 🎯 Quick Start

Once installed, trigger the skill in any Claude conversation:

**Example 1 - SOR generation:**
```
"帮我生成一个 6.6kW 车载充电机 OBC 的技术要求书，
输入 AC 220V，输出给 400V 电池包充电，效率要求 >95%"
```

**Example 2 - Technical solution:**
```
"给电动摩托车设计一个 3kW 充电器技术方案，
输入 220V AC，输出 60V DC 给锂电池充电"
```

**Example 3 - Multiple outputs:**
```
"我需要一套完整的 800V 高压盒技术文档，
包括 SOR、方案书、规格书和原理图"
```

## 📋 Workflow

The skill follows a structured 4-step process:

1. **Step 0 - Requirements diagnosis**: Clarifies product, outputs, scenario, constraints
2. **Step 1 - Solution background**: Project positioning, system boundaries, operating modes
3. **Step 2 - Topology selection**: Matches topologies from library with justification
4. **Step 3 - Parameter specification**: Builds parameter tables with sanity checks
5. **Step 4 - Complete documents**: Outputs full documentation with diagrams

## 🔬 Key Features

### Engineering-Grade Accuracy
- **High-voltage safety elements**: K1/K2 contactors, pre-charge (R_pre+K_pre), fuse, MSD, IMD, HVIL, PE grounding, Y-capacitors, creepage distance annotations
- **Topology self-consistency**: No shoot-through, transformer polarity marked, rectifier orientation correct
- **Sanity checks**: Power balance, voltage/current stress, magnetics saturation, efficiency chain, thermal margin

### Comprehensive Knowledge Base
- **6 Topology Families**: Buck, Boost, Flyback, DAB, LLC, CLLLC, Vienna, Totem-Pole PFC, Inverters (2-level/3-level)
- **10 Advanced Models**: Totem-Pole GaN PFC, MMC HVDC, PMSM FOC, VSG, FCS-MPC, ADRC, TPS-DAB, Interleaved Boost, STATCOM
- **Automotive Standards**: GB/T 18384, ISO 6469, ISO 26262, IEC 61851, and 30+ more

## 🛠️ Optional: Word Export

Generate Word documents from the Markdown output:

```bash
# Install dependency
pip install python-docx

# Export
python scripts/gen_docx.py input.md output.docx --title "6.6kW OBC SOR"
```

The example in `examples/example-obc-6kw6-sor.md` can be converted to demonstrate:
- 59 paragraphs extracted
- 4 tables preserved
- Mermaid diagrams preserved as source code blocks

## 📚 Reference File Usage

The skill automatically loads reference files as needed:
- `topology-library.md` - loaded during Step 2 (topology selection)
- `control-strategies.md` - loaded when specifying control algorithms
- `parameter-guide.md` - loaded during Step 3 (parameter validation)
- `diagram-guide.md` - loaded during diagram generation (Step 2/4)
- `sor-template.md` - loaded when generating SOR documents

You can customize these files to add:
- Proprietary topologies
- In-house control algorithms
- Company-specific standards
- Custom templates

## 🎓 Example Output Structure

See `examples/example-obc-6kw6-sor.md` for a complete example containing:

1. Cover & metadata (version, approvals, change log)
2. Overview (scope, project info, timeline)
3. General description (solution background, operating modes, key conclusions)
4. **Electrical schematics** (topology, HV schematic, circuit schematic with Mermaid)
5. Parameter requirements (input/output/performance tables with sanity checks)
6. Signal definitions (connector pinout tables)
7. Test item list
8. Restricted substances & deliverables
9. Appendices (standards, test details)

## ⚠️ Important Notes

1. **Diagram accuracy is paramount**: The skill will mark uncertain values as "TBD" rather than inventing incorrect specifications
2. **HV safety elements are mandatory**: Any high-voltage system must include all 9 safety elements (contactors, pre-charge, fuse, MSD, IMD, HVIL, grounding, Y-caps, creepage annotations)
3. **Default values**: When information is missing, the skill provides industry-typical defaults clearly marked as "(default, pending confirmation)"
4. **Verification recommendations**: All documents include a note that final designs should be verified through simulation and prototyping

## 🤝 Support & Contribution

- **GitHub**: [Create an issue](https://github.com/your-repo/power-electronics-spec/issues) for bug reports or feature requests
- **Contribute**: Submit PRs for new topologies, standards, or examples
- **Share**: Tag your projects with `#pe-spec-generator`

## 📜 License

MIT License - see LICENSE file for details

---

**Created: 2026-07-07**
**Skill Version: 1.0.0**
**Compatible with: Claude Opus/Sonnet (tested on Claude Desktop)**
