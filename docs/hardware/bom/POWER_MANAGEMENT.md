# Phase 0 Power Management Architecture

**OBD-II power input, 5V regulation, battery backup, and uptime guarantees for bus nodes**

Version: 1.0  
Last Updated: 2026-06-10

---

## System Overview

```
┌─────────────────────────────────────────┐
│      Bus 12V Battery (Vehicle)          │ ← Primary power source
└────────────────────┬────────────────────┘
                     │ (OBD-II connector)
                     ▼
        ┌────────────────────────┐
        │  OBD-II Harness        │
        │  (12V power, Y-split)  │
        │  + Fused (15A)         │
        └────────┬───────────────┘
                 │ Regulated 12V
                 ▼
    ┌────────────────────────────────┐
    │  Buck Converter (12V → 5V)     │
    │  (10A rated, 85% efficiency)   │
    └────────┬───────────────────────┘
             │ 5V regulated output
             ▼
    ┌────────────────────────────────┐
    │  Power Distribution Board      │
    │  (fuse, polarity protection)   │
    └────┬──────────────┬────────────┘
         │              │
         ▼              ▼
    ┌──────────┐   ┌──────────────────┐
    │ RPi CM4  │   │ Battery UPS Hat   │
    │ + modem  │   │ (5V, 10 Ah, auto │
    │ + camera │   │  switchover)      │
    └──────────┘   └──────────────────┘
```

---

## Primary Power Source: OBD-II Connector

### OBD-II Harness Pinout (16-pin)

| Pin | Signal | Voltage | Usage | Our Use |
|-----|--------|---------|-------|---------|
| **2** | J1962 Power | 12V | Ignition-switched power | ✗ (no ignition dependency) |
| **4** | Chassis Ground | GND | Return path | ✓ Ground reference |
| **5** | Signal Ground | GND | CAN/K-line ground | ✓ Ground reference |
| **6** | CAN High | Variable | CAN bus (high) | ⚠ Phase 1 (speed data) |
| **14** | CAN Low | Variable | CAN bus (low) | ⚠ Phase 1 (speed data) |
| **16** | Vbat | 12V constant | Battery power (always-on) | ✓ Primary power |

**Power Extraction (Pins 4 + 16):**
- **Positive (Pin 16):** 12V, always-on (vehicle battery)
- **Negative (Pin 4):** Chassis ground
- **CAN Bus (Pins 6 + 14):** Reserved Phase 1 (OBD speed verification)

### OBD-II Y-Harness Assembly

```
OBD-II Connector
    │
    ├─────────► CAN High/Low (thin pair, to future CAN hat, Phase 1)
    │
    └─────────► Power Module
                  │
                  ├─ Fuse holder (15A, ATO automotive)
                  │   └─ Fuse link for safety (hot-swap protection)
                  │
                  └─ Ring terminal connectors
                      ├─ Positive (red, 10 AWG wire)
                      └─ Negative (black, 10 AWG wire)
```

**Wire Gauge Selection:**
- OBD-II connector → Buck converter: **10 AWG** (5m max run, <3% voltage drop)
- Power dissipation at max current: P = I²R = 10² × 0.0009 = 0.09W (negligible)

**Fuse Protection:**
- Fuse rating: **15A automotive (ATO type)**
- Breaks circuit if current exceeds 15A (protects wiring, buck converter)
- Trip time: <1 sec at 300% rated current (safe for electrical fault)

---

## Power Regulation: 12V → 5V Buck Converter

### Component Spec

**Part:** Generic synchronous buck converter (10A rated)  
**Input:** 12V (vehicle battery, 10–16V operating range)  
**Output:** 5V ± 0.5V regulated  
**Max Current:** 10A (50W output)  
**Efficiency:** 85–92% (minimal heat dissipation)

### Circuit Diagram (Simplified)

```
12V In ──┬─────────┐
         │         │
         │    Q1   │  MOSFET (sync buck switch)
         │   ││    │
         │   ││    │
Vin ────┤     ├──┬─ Inductor L ──┬─ Out
        │     │  │               │
        │    Q2  │  Schottky D   │
        │   ││   │               │
        │   ││   └───────────────┼─ 5V (regulated)
        │         │              │
        └─────────┴──────────────┴─ GND
```

**Key Components:**
- **MOSFETs (Q1, Q2):** Infineon OptiMOS (low RDSon, <50mΩ)
- **Inductor (L):** 10 µH ferrite core (handles 10A peak current)
- **Schottky Diode:** 10A rated (fast reverse recovery)
- **Feedback Circuit:** TL431 (precision voltage reference)

### Operating Characteristics

| Condition | Output | Ripple | Efficiency |
|-----------|--------|--------|------------|
| **Full Load (10A @ 5V)** | 5.0V | 40 mV | 92% |
| **Half Load (5A @ 5V)** | 5.0V | 20 mV | 90% |
| **Idle (0A)** | 5.0V | 10 mV | 50% (quiescent loss) |
| **Cold Start (−5°C)** | 4.8V | 60 mV | 85% (thermal lag) |

**Thermal Performance:**
- Power dissipation (50W in, 42.5W out): 7.5W lost as heat
- Heatsink: Aluminum 50×50mm (θJA ≈ 5°C/W)
- Junction temperature rise: 7.5 W × 5°C/W = 37.5°C above ambient
- At +50°C ambient: Total ~88°C (safe, within 125°C max)

---

## Power Distribution Board (PDN)

**Function:** Aggregate buck converter output, distribute to loads, add protection layers.

### Schematic (Simplified)

```
Buck Out (5V) ──┬─────────────────┬────────────────────┐
                │                 │                    │
         ┌──────┴──────┐   ┌──────┴──────┐      ┌──────┴──────┐
         │ Polarity    │   │ TVS Diode   │      │ Capacitors  │
         │ Protection  │   │ (5.8V)      │      │ (100 µF)    │
         │ (Diode)     │   │ (surge)     │      │             │
         └──────┬──────┘   └──────┬──────┘      └──────┬──────┘
                │                 │                    │
                └────────────┬────┴────────────────────┘
                             │ 5V distribution rail
                    ┌────────┴────────┐
                    │                 │
              ┌─────┴──┐         ┌────┴─────┐
              │ Fuse 1 │         │ Fuse 2   │
              │  5A    │         │  2A      │
              └─────┬──┘         └────┬─────┘
                    │                │
              ┌─────▼────────┐ ┌─────▼────────┐
              │ RPi CM4      │ │ Modem+Camera │
              │ (≤2A)        │ │ (≤2A)        │
              └──────────────┘ └──────────────┘
```

**Component Details:**

| Part | Rating | Purpose |
|------|--------|---------|
| **Polarity Diode** | 1N4148 | Reverse-polarity protection (prevents magic smoke) |
| **TVS Diode** | SP3012-2BTG (5.8V) | Clamp voltage spikes >5.5V (inductive kick from modem) |
| **Bulk Capacitor** | 100 µF × 2 (in parallel) | Stabilize voltage sags during 10A current draw |
| **Fuse 1** | 5A (for RPi + modem) | Protects compute subsystem |
| **Fuse 2** | 2A (for camera) | Protects camera power (USB, isolated) |

---

## Battery Backup (UPS Hat)

### Purpose

**Problem:** Vehicle ignition off for 10+ minutes → OBD-II power lost → node stops recording → missed violations.

**Solution:** Battery UPS hat provides 2–4 hours of backup power, maintains local buffering during connectivity loss.

### Hardware Spec

**Part:** Generic 5V 10 Ah lithium UPS module (with auto-switchover circuit)

**Chemistry:** Li-ion 18650 cells (2S5P configuration, 10 cells total)
- Series: 2× (3.7V → 7.4V nominal)
- Parallel: 5× (1.8 Ah per cell → 9 Ah total)
- Nominal: 7.4V / 5A max discharge = 9 Ah effective (accounting for protection circuit)

**PCM (Protection Circuit Module):**
- Overcharge protection: Cutoff at 8.4V (2.1V per cell)
- Overdischarge protection: Cutoff at 6.0V (3.0V per cell, max safe discharge)
- Over-current protection: Cutoff at 20A (emergency dump, protects cells)
- Temperature sensor: Disable charging below 0°C / above 45°C

### Auto-Switchover Circuit

**Topology:** Ideal Diode (Schottky + MOSFET gate drive)

```
        12V ────┐
                │
            ┌───┴────┐
            │ Schotty │  (ideal diode)
            │   D1    │
            │  │\__   │
            │  │______|─── + 5V to Load
            │         │
        ┌───┴─────┐   │
        │  UPS    │   │
        │ Battery │──┘
        └─────────┘
```

**Operation:**
- **Vehicle ON (12V available):** Buck converter feeds load (5V), charges battery via 1A charger
- **Vehicle OFF (12V lost):** Ideal diode switches to battery, provides 5V backup (via internal boost circuit, 7.4V → 5V)
- **Switching latency:** <5 ms (imperceptible to RPi, continuous 5V rail)

### Runtime Calculation

**Power Budget (backup mode):**
- Raspberry Pi CM4: 1.5W (idle, no GPU)
- Modem (LTE sleep mode): 0.2W (registered, idle)
- Sensors (GPS, OBD, TPM): 0.3W (polling only, no TX)
- **Total backup draw:** ~2.0W @ 5V = 0.4A

**Battery capacity:** 9 Ah @ 5V (45 Wh)

**Runtime:** 45 Wh ÷ 2.0W = **22.5 hours continuous backup**

**Conservative estimate (account for efficiency losses, end-of-discharge curve):**
- Practical runtime: ~15–18 hours
- Phase 0 requirement: **2–4 hours** (covers overnight + early morning)
- ✓ Exceeds requirement

**Evidence Buffering (Phase 0 MVP):**
- Local buffer: 60 GB on eMMC (RPi CM4 comes with 4 GB, but supplementary storage possible)
- At 10 Mbps H.264: 60 GB ÷ (10 Mbps / 8) = 48,000 seconds ≈ 13.3 hours
- Battery lasts 15 hr, buffer lasts 13.3 hr → Sufficient (upload resumes when vehicle powers on)

---

## Load Characteristics

### RPi CM4 Power Consumption

| Mode | Voltage | Current | Power | Notes |
|------|---------|---------|-------|-------|
| **Idle (no load)** | 5.0V | 0.3A | 1.5W | No GPU, sleep timers active |
| **Camera capture** | 5.0V | 0.6A | 3.0W | USB camera streaming |
| **Inference (3 fps)** | 5.0V | 0.9A | 4.5W | TensorFlow Lite models running |
| **Peak (all subsystems)** | 5.0V | 1.2A | 6.0W | Rare, transient |

**Average (Phase 0 operation):** ~3.5W

### Modem (Quectel EC25) Power

| State | Current | Power | Notes |
|-------|---------|-------|-------|
| **Off** | <10 mA | 0.05W | No radio activity |
| **Idle (registered)** | 50 mA | 0.25W | Maintaining cell connection |
| **Transmit (full power)** | 400 mA | 2.0W | Upload operation (peak) |
| **Receive (DL)** | 150 mA | 0.75W | Download/API responses |

**Average (Phase 0):** ~0.3W (idle + periodic heartbeat)

### Camera + USB Hub

| Component | Current | Power |
|-----------|---------|-------|
| **USB Dashcam (1440p capture)** | 500 mA | 2.5W |
| **USB hub (buffer, optional)** | 50 mA | 0.25W |

**Total system average (continuous operation):** ~6W

---

## Charging Strategy

### When Vehicle Powered On

**Charger Input:** 12V (OBD-II)  
**Charger Output:** 5V @ 1A (dedicated charging circuit, separate from buck converter output)

**Charging Profile:**
- **Bulk charge phase:** Constant 1A @ <8.4V (until ~85% SOC)
- **Taper charge phase:** Constant voltage 8.4V, decreasing current (until <50 mA)
- **Float charge:** 8.1V trickle (maintains capacity, prevents overcharge)

**Charging time (0→100%):** ~9 hours (10 Ah ÷ 1A + taper curve)

**Scenario:** OC Transpo bus operates 16 hr/day, parked 8 hr/night
- Daily charge available: 8 hr × 1A = 8 Ah
- Daily drain (Phase 0 MVP): ~0.4A × 2 hr (backup usage rare) = 0.8 Ah
- Net charge: +7.2 Ah per day → Battery full within 2 days

---

## Thermal Management

### Heat Dissipation Paths

```
Junction (MOSFET) ──┬─ PCB thermal plane (0.05°C/W)
                    │
                    ├─ Heatsink (5°C/W)
                    │
                    ├─ Enclosure wall (aluminum)
                    │
                    └─ Ambient air (15°C/W)

Total θJA ≈ 25°C/W (conservative estimate)
```

**Worst-case scenario:** Full load (50W input, 42.5W out, 7.5W heat) @ +50°C ambient
- ΔT = 7.5W × 25°C/W = 188°C rise (FAILURE)
- **Mitigation:** Reduce load or improve heatsinking (thermal paste, clip-on fin)

**Realistic scenario:** Average load (6W total system, 1W loss) @ +40°C ambient
- ΔT = 1W × 25°C/W = 25°C rise → Junction ~65°C (SAFE)

### Thermal Management (Phase 0)

**Passive cooling sufficient** if:
1. Aluminum enclosure mounted with thermal contact to node exterior
2. Cable entry glands allow some convection
3. Node not in direct sunlight (cover with reflective tape if parked in sun)

**Phase 1 option:** Add small 5V fan (0.3W) for active cooling in high-temp scenarios

---

## Fault Scenarios & Recovery

### Scenario 1: OBD-II Connector Loose/Corroded

**Symptom:** Intermittent 12V power loss, voltages fluctuate 8–15V

**Detection:** Buck converter voltage monitoring, triggers alert if Vin < 10V for >5 sec

**Recovery:**
- Firmware: Log power disruptions (audit trail)
- Failover to battery (if available)
- Buffer evidence locally (camera keeps recording)
- Upload queued clips when 12V restored

**Timeline:**
- T+0: Connector loosens, Vin drops
- T+5 sec: Firmware detects Vin < 10V, switches to battery
- T+5 sec to T+24hr: Run on battery, buffer evidence
- T+24hr: Vehicle powered on again, 12V restored, uploads resume
- ✓ Zero evidence loss

### Scenario 2: Battery Discharge During Extended Downtime

**Symptom:** Vehicle parked for 2+ weeks, battery fully discharged, no 5V power

**Recovery:**
- Next vehicle power-up: Buck converter boots RPi from 12V
- RPi firmware checks battery SoC (via PCM fuel gauge)
- If SoC < 10%: Skip evidence uploads (focus on safety), charge battery for 4 hrs before normal operation
- Battery recovers to usable capacity (not 100% after deep discharge, but sufficient for 2–4 hr backup)

**Prevention:** OC Transpo maintenance schedule includes monthly battery top-off (connect charger during winter)

### Scenario 3: Modem Power Surge (LTE burst transmission)

**Symptom:** Modem draws 400 mA peak @ 5V, causes <100 mV voltage sag

**Protection:**
- Bulk capacitors (100 µF) absorb current surge, smooth voltage
- TVS diode clamps overvoltage transients
- Fuse 2 (2A) breaks if sustained overcurrent (indicates fault)

**Impact:** RPi unaffected (capacitors maintain 5V±0.5V), modem operates normally

---

## Monitoring & Diagnostics

### Voltage Monitoring (RPi GPIO)

**Analog input (via ADC hat, Phase 1):**
```python
import board
import busio
import adafruit_ads1x15.analog_in as AnalogIn
from adafruit_ads1x15.ads1115 import ADS1115

i2c = busio.I2C(board.SCL, board.SDA)
ads = ADS1115(i2c)

# Monitor 12V rail (via 1:4 voltage divider)
channel_12v = AnalogIn(ads, ads.P0)
v_12v = channel_12v.voltage * 4  # Scale back to 12V

# Monitor 5V rail
channel_5v = AnalogIn(ads, ads.P1)
v_5v = channel_5v.voltage

# Log periodically
if v_12v < 10.0 or v_5v < 4.8:
    logger.warning(f"Low voltage: 12V={v_12v}, 5V={v_5v}")
```

**Data logged to audit_log:**
- Timestamp, 12V voltage, 5V voltage, state (vehicle_on/battery/fault)
- Helps diagnose power delivery issues post-deployment

### Battery Health Monitoring (Phase 1+)

**Smart BMS (Battery Management System):**
- Fuel gauge IC (TI bq27441) tracks SoC (State of Charge)
- Communicates via I²C to RPi
- Estimates time-to-empty, cycle count, temperature

**Firmware action:**
- SoC < 5%: Disable non-critical systems (ALPR, lane detection)
- SoC < 2%: Graceful shutdown (avoid data corruption)
- Cycle count > 500: Alert maintenance (battery end-of-life)

---

## Design Margins & Safety Factors

| Parameter | Target | Safety Margin | Rationale |
|-----------|--------|---------------|-----------|
| **Max input voltage** | 16V | → 20V TVS clamp | Accounts for alternator overshoot |
| **Fuse rating** | 15A | 1.5× safety factor | Protects wiring before MOSFETs fail |
| **Capacitor ripple current** | 2A RMS | → 5A cap rating | Electrolytic derating (80% operational) |
| **Thermal junction limit** | 65°C | → 125°C absolute max | 2× safety margin |
| **Battery discharge cutoff** | 6.0V (3.0V per cell) | → 2.8V absolute min | Prevents LiPo battery damage |
| **Switchover latency** | <5 ms | → <1 ms practical | Prevents RPi brownout reboot |

---

## Bill of Materials (Power Subsystem)

| Component | Qty | Cost | Notes |
|-----------|-----|------|-------|
| **OBD-II Harness (Y-split)** | 1 | $15 | 12V + CAN lines |
| **Buck Converter (12V→5V, 10A)** | 1 | $25 | Synchronous MOSFET type |
| **Battery UPS Hat (5V, 10 Ah)** | 1 | $65 | Li-ion 2S5P configuration |
| **Power Distribution Board** | 1 | $15 | Fuses, diodes, caps |
| **Heatsink (50×50 mm)** | 1 | $8 | Aluminum, self-adhesive |
| **Connectors + Cables** | — | $8 | Terminal blocks, Anderson connectors |
| **Thermal paste + mounting** | — | $3 | Transfer pad, hardware |
| **Testing/QA labor** | — | $5 | Power-on verification |
| | | | |
| **Subtotal (Power Subsystem)** | | **$144** | ~30% of total node BOM |

---

## Testing Procedure (Factory QA)

### Power-On Test

```bash
1. Connect 12V test supply (OBD-II harness simulation)
2. Measure 5V rail voltage: Expected 5.0V ± 0.5V
3. Measure 12V rail voltage: Expected 12V ± 1V (with some ripple)
4. No fuses blown: Check continuity of fuse holders
5. RPi boots to login: Indicates stable power, no brownouts
6. Battery UPS engaged: Monitor switches from buck to battery when 12V disconnected
```

### Battery Endurance Test

```bash
1. Charge battery to 100% SoC (confirm via fuel gauge, if installed)
2. Connect 5V load (RPi + modem simulation): 0.5A draw
3. Disconnect 12V source, run on battery for 4 hours
4. Measure battery SoC: Expected ≥50% (conservative, accounts for aging)
5. Measure RPi uptime: Expected no reboots, stable 5V ± 0.2V
```

---

## References

- [PHASE_0_MVP_ARCHITECTURE.md § Edge Node Layer](./PHASE_0_MVP_ARCHITECTURE.md#edge-node-layer)
- [OBD-II Connector Specification (SAE J1962)](https://en.wikipedia.org/wiki/On-board_diagnostics)
- [Raspberry Pi CM4 Power Requirements](https://www.raspberrypi.com/products/compute-module-4/?variant=raspberry-pi-cm4000000)
- [Quectel EC25 Power Consumption Table](https://www.quectel.com/product/lte-ec25e/)

---

**Version:** 1.0  
**Last Updated:** 2026-06-10  
**Maintainer:** CivicMesh Hardware Team  
**License:** AGPL-3.0 (CivicMesh core)
