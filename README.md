
# ESAAR Quant Engine
### High-Throughput Event-Driven Quantitative Execution & Dynamic Risk Management Architecture

[![Python Version](https://img.shields.io/badge/python-3.10%2B-blue.svg)](https://www.python.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Architecture](https://img.shields.io/badge/Architecture-Event--Driven%20Async-green.svg)]()
[![Platform](https://img.shields.io/badge/Platform-Linux%20Cloud%20VPS-orange.svg)]()

ESAAR Quant Engine is an institutional-grade, low-latency algorithmic execution system designed for high-throughput digital asset perpetual markets. The engine continuously ingests real-time Level-2 order book depth, calculates dynamic volatility regimes via Wilder's ATR, and enforces multi-timeframe macro trend gates with asymmetric risk management.

---

## 🏛 Architectural Overview
                ┌─────────────────────────────────────────┐
                  │   Real-Time Market Ingestion (WS/REST)  │
                  └────────────────────┬────────────────────┘
                                       │
                ┌──────────────────────┴──────────────────────┐
                ▼                                             ▼
 ┌─────────────────────────────┐               ┌─────────────────────────────┐
 │  Level-2 50-Depth OBI Gate  │               │   4H Dual-Layer Macro Gate  │
 │   (|OBI| >= 0.25 to 0.38)   │               │   (50 EMA & 200 EMA Anchor) │
 └──────────────┬──────────────┘               └──────────────┬──────────────┘
                │                                             │
                └──────────────────────┬──────────────────────┘
                                       │
                                       ▼
                  ┌─────────────────────────────────────────┐
                  │     Adaptive Volatility Classifier      │
                  │  ATR Ratio: Money Maker vs Capital Saver│
                  └────────────────────┬────────────────────┘
                                       │
                                       ▼
                  ┌─────────────────────────────────────────┐
                  │      4-Stage Progressive Ratchet        │
                  │   (+0.85% BE ➔ +1.35% ➔ +2.40%+ Trail)   │
                  └────────────────────┬────────────────────┘
                                       │
                                       ▼
                  ┌─────────────────────────────────────────┐
                  │  Fault-Tolerant State & Telegram Alert  │
                  └─────────────────────────────────────────┘
                  
---

## ⚡ Core Quantitative Capabilities

### 1. Market Microstructure Order Book Imbalance (OBI)
Evaluates real-time Level-2 order book dominance across 50 bid/ask levels to detect institutional absorption:
$$\text{OBI} = \frac{\sum_{i=1}^{50} V_{\text{bid}, i} - \sum_{i=1}^{50} V_{\text{ask}, i}}{\sum_{i=1}^{50} V_{\text{bid}, i} + \sum_{i=1}^{50} V_{\text{ask}, i}}$$

### 2. Dual-State Adaptive Volatility Switching
The engine automatically shifts its operational posture based on real-time volatility expansion:
$$\text{ATR Ratio} = \frac{\text{ATR}_{15m}(\text{Current})}{\text{EMA}(\text{ATR}_{15m}, 20)}$$
* **Money Maker Mode ($\text{ATR Ratio} \ge 1.15\text{x}$)**: Expands profit ceilings, loosens entry thresholds (`OBI >= 0.25`), and activates multi-stage trailing runners up to $+6.00\%$.
* **Capital Saver Mode ($\text{ATR Ratio} < 0.85\text{x}$)**: Engages the Chop-Shield, raising OBI requirements to `0.38` (2.2:1 institutional wall), enforcing `VCR >= 1.60x`, or locking into 100% cash defense to prevent micro-fakeout drawdowns.

### 3. Dual-Layer 4-Hour Macro Confluence
Anchors directional momentum against higher-timeframe institutional boundaries:
* **BULL Regime**: $\text{Price} > \text{EMA}_{4H}(200)$ — long breakouts permitted.
* **BEAR Regime**: $\text{Price} < \text{EMA}_{4H}(50)$ — strict block on counter-trend longs.
* **CONFLICT Zone**: Between 50 & 200 EMA — risk-off positioning.

### 4. 4-Stage Progressive Profit Architecture
* **Stage 1 (Zero-Loss Shield)**: At $+0.85\%$ gain, Stop-Loss ratchets to entry $+0.15\%$ (eliminates downside).
* **Stage 2 (Core Cash Lock)**: At $+1.35\%$ gain, Stop-Loss locks $+0.90\%$ profit.
* **Stage 3 (Climax Lock)**: At $+1.90\%$ gain, Stop-Loss locks $+1.50\%$ profit.
* **Stage 4 (Peak Runner Trail)**: Above $+2.40\%$ gain, dynamically trails $-0.50\%$ behind highest peak price.

---

## 📁 Repository Structure
```text
esaar-quant-engine/
├── core/
│   ├── macro_confluence.py       # 4H Multi-Timeframe Trend Classifier
│   ├── microstructure_obi.py     # L2 50-Depth Order Book Imbalance Engine
│   ├── volatility_regime.py      # Real-time Wilder's ATR & Regime Switch
│   └── progressive_ratchet.py    # 4-Stage Peak Trailing Logic
├── execution/
│   ├── market_scanner.py         # 100+ Liquid Perpetuals Scanner
│   └── position_manager.py       # Asymmetric Risk Allocation & Sizing
├── telemetry/
│   ├── state_persistence.py      # JSON State Recovery & Zero-Downtime Hot-Swap
│   └── telegram_notifier.py      # Asynchronous Scorecard & Fill Dispatcher
├── config.py                     # Risk Parameters, Keys & Thresholds
├── main.py                       # High-Performance Event Loop Entrypoint
├── requirements.txt              # Production Dependencies
└── README.md                     # Architecture Documentation
# Clone the repository
git clone https://github.com/sandeeprawani6111-netizen/esaar-quant-engine.git
cd esaar-quant-engine
# Create virtual environment
python3 -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
