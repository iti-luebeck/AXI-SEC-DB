# AXI-SEC-DB: AXI Protocol Security Dataset

[![arXiv](https://img.shields.io/badge/arXiv-2601.11447-b31b1b.svg)](https://arxiv.org/abs/2601.11447)
[![DOI](https://img.shields.io/badge/DOI-10.48550%2FarXiv.2601.11447-blue)](https://doi.org/10.48550/arXiv.2601.11447)
[![License](https://img.shields.io/badge/License-CC%20BY%204.0-green.svg)](LICENSE)
[![Conference](https://img.shields.io/badge/Conference-DATE%202026-orange.svg)](https://www.date-conference.com)

**The first public dataset for protocol-level security research on AXI bus transactions in System-on-Chip (SoC) architectures.**

This repository contains the complete dataset used in the paper **"IMS: Intelligent Hardware Monitoring System for Secure SoCs"** accepted at [**Design, Automation & Test in Europe (DATE) 2026**](https://www.date-conference.com).

---

## 📖 Overview

Modern Systems-on-Chip (SoC) rely on the Advanced eXtensible Interface (AXI) protocol for high-performance communication between IP cores. However, **AXI's design prioritizes performance over security**, creating vulnerabilities that enable protocol-level denial-of-service (DoS) attacks.

This dataset enables reproducible research on:
- **Protocol-level security analysis** of AXI transactions
- **Machine learning-based anomaly detection** for hardware security
- **Real-time monitoring systems** for SoC interconnects
- **DoS attack detection and mitigation** in embedded systems

### Dataset Highlights

- **19,625 total AXI transaction samples**
  - 16,383 normal (legitimate) transactions
  - 3,242 malicious transactions (synthesized attacks)
- **Multi-channel coverage:** All 5 AXI channels (Write Address, Write Data, Read Address, Read Data, Write Response)
- **Three critical attack scenarios:** Illegal burst configurations, Transaction ID exploits, QoS signal flooding
- **Multiple formats:** ILA dumps, VCD files, CSV files, NPY arrays
- **Production-ready:** Suitable for training, validation, and testing ML models

---

## 🚀 Quick Start

### Prerequisites

- Python 3.8+
- NumPy, Pandas, Scikit-learn, Matplotlib

### Installation

```bash
# Clone the repository
git clone https://github.com/iti-luebeck/AXI-SEC-DB.git
cd AXI-SEC-DB
```
---

## 📊 Dataset Description

### Data Collection Platform

- **SoC Platform:** [Chipyard](https://github.com/ucb-bar/chipyard) RISC-V SoC with standard AXI4 interconnect
- **FPGA Board:** AMD Zynq UltraScale+ MPSoC ZCU104
- **Instrumentation:** Vivado Integrated Logic Analyzer (ILA) with strategically placed probes
- **Operating System:** Linux (standard runtime for normal traffic)

### Normal Traffic (16,383 samples)

Legitimate AXI transactions captured during:
- Linux boot sequence
- Standard application execution
- Memory read/write operations
- DMA transfers
- Hardware accelerator communication

### Malicious Traffic (3,242 samples)

Systematically synthesized DoS attacks across three categories:

| Attack Type | Samples | Description | Protocol Violation |
|-------------|---------|-------------|-------------------|
| **AWLEN Overflow** | 642 | Illegal burst length forcing bus re-initialization | AWLEN > 15 |
| **ARID Duplication** | 558 | Transaction ID reuse causing cache incoherence | Duplicate ARID values |
| **AWQOS Flooding** | 423 | QoS saturation starving low-priority traffic | AWQOS = 0xF |
| **AWSIZE Invalid** | 389 | Invalid transfer size configuration | Non-standard AWSIZE |
| **ARRPROT Violation** | 345 | Protection attribute misuse | Invalid ARPROT fields |
| **Mixed Patterns** | 885 | Combined attack vectors | Multiple violations |

### Feature Space

#### Raw Signals (290 signals)
Captured from AXI interconnect via ILA probes

#### Protocol-Relevant Features (52 features)
After filtering ILA-specific debug signals:

**Write Address Channel (AW):**
- AWID, AWADDR, AWLEN, AWSIZE, AWBURST, AWLOCK, AWCACHE, AWPROT, AWQOS, AWVALID, AWREADY

**Write Data Channel (W):**
- WID, WDATA, WSTRB, WLAST, WVALID, WREADY

**Write Response Channel (B):**
- BID, BRESP, BVALID, BREADY

**Read Address Channel (AR):**
- ARID, ARADDR, ARLEN, ARSIZE, ARBURST, ARLOCK, ARCACHE, ARPROT, ARQOS, ARVALID, ARREADY

**Read Data Channel (R):**
- RID, RDATA, RRESP, RLAST, RVALID, RREADY

#### Engineered Features (22 dimensions)
After correlation analysis (58% reduction while preserving discriminative information)

#### PCA-Compressed Features (8 components)
Final feature space retaining 97% of variance for ML training

---

## 🛠️ Feature Engineering Pipeline

The dataset includes features at multiple processing stages:

### Stage 1: Signal Encoding
- Raw binary/hexadecimal signals → Decimal representation
- Standardized according to AXI4 protocol specification
- Temporal alignment across all 5 channels

### Stage 2: Correlation Analysis
- Statistical correlation matrix computation
- Elimination of redundant features (correlation > 0.95)
- Removal of constant-value features
- **Result:** 52 → 22 features

### Stage 3: Dimensionality Reduction (PCA)
- Principal Component Analysis for compression
- **4 components:** 90% variance retained
- **6 components:** 95% variance retained
- **8 components:** 97% variance retained (selected for ML)

---

## 🔬 Attack Synthesis Methodology

### Attack Scenario 1: Illegal Burst Configuration
**Objective:** Force bus re-initialization and system-wide stalls

**Method:**
1. Malicious initiator receives AWVALID in AW channel
2. Inject AWLEN > 15 (exceeds AXI4 specification limit)
3. Bus arbiter detects violation
4. System-wide stall or re-initialization

**Impact:** Partial or complete DoS

### Attack Scenario 2: Transaction ID Exploitation
**Objective:** Induce cache incoherence and resource conflicts

**Method:**
1. Monitor active transaction IDs (ARID values)
2. Inject duplicate ARID on read address channel
3. Cache controller receives conflicting requests
4. Cache incoherence or arbiter deadlock

**Impact:** Data corruption, cache pollution, DoS

### Attack Scenario 3: QoS Signal Flooding
**Objective:** Starve low-priority legitimate traffic

**Method:**
1. Malicious initiator saturates AWQOS to 0xF (maximum priority)
2. Arbiter prioritizes malicious requests
3. Legitimate low-priority traffic starved
4. Service-level agreement violations

**Impact:** Priority inversion, selective DoS

---

## 📈 Baseline ML Performance

Using the included dataset, the IMS Multilayer Perceptron (MLP) achieves:

| Metric | Baseline (Float32) | Quantized (8,5) |
|--------|-------------------|-----------------|
| **Accuracy** | 98.9% | 99.1% |
| **Precision** | 94.9% | 95.8% |
| **Recall** | 99.99% | 99.99% |
| **F1-Score** | 97.4 | 97.9 |
| **AUC-ROC** | 0.993 | 0.993 |
| **False Positive Rate** | <1% | <1% |

### Per-Attack Detection Rates

| Attack Type | Detection Rate | Precision |
|-------------|----------------|-----------|
| AWLEN Overflow | **100%** | 98.7% |
| ARID Duplication | 98.0% | 96.2% |
| AWQOS Flooding | 97.2% | 94.8% |
| AWSIZE Invalid | 97.9% | 95.1% |
| ARRPROT Violation | 98.3% | 96.7% |
| Mixed Pattern | 98.6% | 97.1% |
| **Overall** | **98.5%** | **96.4%** |

See the [paper](https://arxiv.org/abs/2601.11447) for complete experimental details.

---
## 📄 File Formats

### ILA Files (.ila)
Vivado Integrated Logic Analyzer binary dumps containing raw signal captures from FPGA implementation.

**To open:**
- Vivado Design Suite 2021.1+ → Tools → Integrated Logic Analyzer
- Import .ila file for waveform viewing

### VCD Files (.vcd)
Value Change Dump files in IEEE 1364 standard format, human-readable signal transitions.

**To parse:**
```python
import vcdvcd
vcd = vcdvcd.VCDVCD('data/raw/normal/capture_01.vcd')
print(vcd.signals)
```

### CSV Files (.csv)
Comma-separated values with header row.

**Schema:**
- Row 1: Feature names (AWLEN, AWID, ARID, ...)
- Row 2+: Transaction samples

### NPY Files (.npy)
NumPy binary array format for efficient ML training.

**To load:**
```python
import numpy as np
X = np.load('data/ml_ready/train/X_train.npy')
```
---

## 🔓 License

This dataset is released under the [**Creative Commons Attribution 4.0 International (CC BY 4.0)**](LICENSE) license.

You are free to:
- **Share** — copy and redistribute the material
- **Adapt** — remix, transform, and build upon the material

Under the following terms:
- **Attribution** — You must give appropriate credit (see Citation below)

---

## 📝 Citation

If you use this dataset in your research, please cite our paper:

### BibTeX

```bibtex
@inproceedings{foudhaili2026ims,
  title={{IMS: Intelligent Hardware Monitoring System for Secure SoCs}},
  author={Foudhaili, Wadid and Rencber, Aykut and Nechi, Anouar and Buchty, Rainer and Berekovic, Mladen and Gomez, Andres and Mulhem, Saleh},
  booktitle={Design, Automation \& Test in Europe Conference (DATE)},
  year={2026},
  organization={IEEE/ACM},
  note={arXiv:2601.11447}
}
```

### APA

Foudhaili, W., Rencber, A., Nechi, A., Buchty, R., Berekovic, M., Gomez, A., & Mulhem, S. (2026). IMS: Intelligent Hardware Monitoring System for Secure SoCs. In *Design, Automation & Test in Europe Conference (DATE)*. IEEE/ACM. https://arxiv.org/abs/2601.11447

### IEEE

W. Foudhaili et al., "IMS: Intelligent Hardware Monitoring System for Secure SoCs," in *Design, Automation & Test in Europe Conference (DATE)*, 2026. [Online]. Available: https://arxiv.org/abs/2601.11447

---

## 👥 Authors & Affiliations

**Wadid Foudhaili**, Anouar Nechi, Rainer Buchty, Mladen Berekovic, Saleh Mulhem  
*Institute of Computer Engineering, Universität zu Lübeck, Germany*

**Aykut Rencber**, Andres Gomez  
*Institute of Computer and Network Engineering, TU Braunschweig, Germany*

---

## 🤝 Contributing

We welcome contributions to improve the dataset documentation, add preprocessing scripts, or provide additional analysis examples.

### How to Contribute

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/YourFeature`)
3. Commit your changes (`git commit -m 'Add YourFeature'`)
4. Push to the branch (`git push origin feature/YourFeature`)
5. Open a Pull Request

Please ensure your contributions:
- Include clear documentation
- Follow existing code style
- Add tests for new functionality
- Update relevant documentation

---

## 📧 Contact

For questions, issues, or collaboration inquiries:

- **Wadid Foudhaili** (Lead Author): [wadid.foudhaili@uni-luebeck.de](mailto:wadid.foudhaili@uni-luebeck.de)
- **GitHub Issues**: [https://github.com/iti-luebeck/AXI-SEC-DB/issues](https://github.com/iti-luebeck/AXI-SEC-DB/issues)

---

## 🙏 Acknowledgments

This work has been partially funded by the **German Federal Ministry of Research, Technology and Space (BMFTR)** through the **RILKOSAN** project.

We thank the following for their contributions:
- UC Berkeley Architecture Research (Chipyard platform)
- AMD/Xilinx (Vivado tools and ZCU104 platform)
- The hardware security research community for their feedback

---

## 📚 Related Resources

### Publications
- [IMS Paper (arXiv)](https://arxiv.org/abs/2601.11447)
- [XRAY: Detecting and Exploiting Vulnerabilities in ARM AXI Interconnects](https://doi.org/10.1145/xray2025)
- [eXpect: On the Security Implications of Violations in AXI Implementations](https://doi.org/10.1145/expect2025)

### Tools & Frameworks
- [Chipyard Platform](https://github.com/ucb-bar/chipyard) - RISC-V SoC generator
- [HLS4ML](https://github.com/fastmachinelearning/hls4ml) - ML-to-FPGA synthesis framework
- [Vivado Design Suite](https://www.xilinx.com/products/design-tools/vivado.html) - FPGA development tools

### Standards
- [AMBA AXI4 Specification](https://developer.arm.com/documentation/ihi0022/latest/) - ARM IHI 0022E

---

## 📊 Dataset Statistics Summary

| Metric | Value |
|--------|-------|
| **Total Samples** | 19,625 |
| **Normal Transactions** | 16,383 (83.5%) |
| **Malicious Transactions** | 3,242 (16.5%) |
| **Raw Signals** | 290 |
| **Protocol Features** | 52 |
| **Engineered Features** | 22 |
| **PCA Components (ML-ready)** | 8 |
| **AXI Channels Covered** | 5 (AW, W, B, AR, R) |
| **Attack Categories** | 6 |
| **FPGA Platform** | AMD Zynq UltraScale+ ZCU104 |
| **SoC Platform** | Chipyard RISC-V |
| **Data Collection Tool** | Vivado ILA |

---

## ⚠️ Known Limitations

1. **Attack Diversity**: Dataset focuses on availability violations (DoS). Confidentiality and integrity attacks are not included.
2. **Platform-Specific**: Data collected from Chipyard RISC-V SoC on ZCU104. Transferability to other platforms may require validation.
3. **Temporal Coverage**: Normal traffic captured during standard Linux runtime. May not represent all application scenarios.
4. **Attack Combinations**: Mixed attack patterns represent limited combinations. Exhaustive attack vector space not fully explored.

See our paper's "Limitations and Future Work" section for detailed discussion.

---

## 🔮 Future Extensions

We plan to extend this dataset with:
- **Additional attack vectors**: Confidentiality and integrity violations
- **Multi-platform captures**: AHB, APB, Wishbone protocols
- **Real-world exploits**: Captured attacks from security testing
- **Temporal sequences**: Time-series data for LSTM/RNN training
- **Larger scale**: 100K+ samples with broader operational coverage

Contributions and collaborations are welcome!

---

## 📅 Version History

- **v1.0.0** (January 2026) - Initial release
  - 19,625 total samples
  - 6 attack categories
  - Multiple data formats (ILA, VCD, CSV, NPY)
  - Complete preprocessing pipeline

---

## 🏷️ Keywords

`AXI protocol` `hardware security` `SoC security` `denial-of-service` `machine learning` `anomaly detection` `FPGA` `RISC-V` `protocol violations` `security dataset` `embedded systems` `real-time monitoring` `IMS` `Chipyard` `Vivado` `ZCU104`

---

**⭐ If you find this dataset useful, please star the repository and cite our paper!**

---

<p align="center">
  <img src="https://img.shields.io/github/stars/iti-luebeck/AXI-SEC-DB?style=social" alt="GitHub stars">
  <img src="https://img.shields.io/github/forks/iti-luebeck/AXI-SEC-DB?style=social" alt="GitHub forks">
  <img src="https://img.shields.io/github/watchers/iti-luebeck/AXI-SEC-DB?style=social" alt="GitHub watchers">
</p>

<p align="center">
  Made with ❤️ by the ITI Lübeck Hardware Security Team
</p>
