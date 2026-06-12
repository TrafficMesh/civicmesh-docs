# Phase 0 Node Hardware Bill of Materials

**Per-vehicle node for OC Transpo buses: bus lane obstruction + red light detection**

Version: 1.0  
Total Cost Range: **$485–$600 USD** (in-volume pricing)  
Target Deployment: 50 OC Transpo buses (Phase 0 pilot)

---

## Summary

| Category | Unit Cost | Qty | Subtotal | Notes |
|----------|-----------|-----|----------|-------|
| **Compute** | | | | |
| Raspberry Pi CM4 (2 GB RAM, eMMC) | $70 | 1 | $70 | Edge inference, event loop |
| **Camera** | | | | |
| USB Dashcam (Front, 1440p, 120°) | $180 | 1 | $180 | H.264 encoding, 30 fps |
| **Vehicle Integration** | | | | |
| OBD-II Dongle (ELM327 clone) | $35 | 1 | $35 | Speed, RPM, GPS, diagnostics |
| GPS Module (u-blox NEO-M9N) | $28 | 1 | $28 | 5 mm accuracy, 30 Hz update |
| LTE Modem (Quectel EC25) | $50 | 1 | $50 | Upload, telemetry, heartbeat |
| **Security** | | | | |
| TPM 2.0 Module (SLB9670) | $18 | 1 | $18 | Hardware key storage, attestation |
| **Power Management** | | | | |
| OBD-II Power Adapter (12V→5V buck) | $25 | 1 | $25 | Vehicle power input |
| Battery Backup (5V, 10 Ah, UPS hat) | $65 | 1 | $65 | 2–4 hr uptime during disconnect |
| Power Distribution Board | $15 | 1 | $15 | Fuse management, voltage regulation |
| **Enclosure & Weatherproofing** | | | | |
| Aluminum Enclosure (150×100×75mm) | $28 | 1 | $28 | IP67 rated, heat dissipation |
| Silicone Gasket + Stainless Hardware | $12 | 1 | $12 | Water/dust sealing |
| DIN Rail + Cable Glands | $10 | 1 | $10 | Internal organization |
| **Cabling & Connectors** | | | | |
| OBD-II Harness (Y-split, fused) | $15 | 1 | $15 | Power + CAN bus |
| USB Cables (USB-C, micro-USB) | $8 | 2 | $16 | Camera, modem, RPi |
| CAT6 Ethernet (optional, for bench testing) | $5 | 1 | $5 | Local network access |
| Terminal Blocks + Connectors | $8 | 1 | $8 | Power distribution |
| **Firmware & Configuration** | | | | |
| NFC Tag (ISO14443A, 1K) | $2 | 1 | $2 | Node enrollment, read-only config |
| Micro-SD Card (32 GB, U3, A2) | $8 | 1 | $8 | OS + application storage |
| **Documentation & Labels** | | | | |
| Tamper-evident Seals | $3 | 5 | $15 | Chain of custody |
| Labels (model, serial, MAC) | $2 | 1 | $2 | Asset tracking |
| **Assembly & QA** | | | | |
| Assembly Labor (30 min @ $50/hr) | | | $25 | Testing, configuration |
| **Shipping & Packaging** | | | | |
| Custom Packaging (protective foam) | $8 | 1 | $8 | Safe delivery to bus depot |
| | | | | |
| **TOTAL (Single Node)** | | | **$529** | Factory cost, ex. taxes |
| **TOTAL (50-unit batch)** | | | **$24,500** | $490/unit at scale |

---

## Detailed Component Specs

### Compute: Raspberry Pi CM4 (2 GB, eMMC)

**Part:** Raspberry Pi Compute Module 4 (CM4104032)  
**Cost:** $70 USD  
**Specs:**
- CPU: Broadcom BCM2711 (ARMv8, 4×1.5 GHz)
- RAM: 2 GB LPDDR4
- Storage: 4 GB eMMC (OS + models)
- GPIO: 28-pin SO-DIMM, I²C, SPI, UART
- Power: 5V @ 2.5A typical

**Why this model?**
- Industrial-grade (wide temp range: −10°C to +60°C)
- eMMC onboard (no SD card wear)
- 2 GB RAM sufficient for TensorFlow Lite models + local buffering
- Well-documented for automotive edge inference

**Alternative:** Raspberry Pi 5 CM module (coming late 2026) for better thermal envelope

---

### Camera: USB 1440p Dashcam

**Part:** Generic USB 1440p dashcam (e.g., Viofo A119 Mini, or OEM equivalent)  
**Cost:** $180 USD  
**Specs:**
- Resolution: 2560 × 1440 @ 30 fps
- Codec: H.264 (hardware-accelerated)
- FoV: 120° (captures bus lane and adjacent lanes)
- Bitrate: 8–10 Mbps (compressed)
- Interface: USB 2.0 (480 Mbps, sufficient for single camera)
- Lens: f/1.8, wide aperture (good low-light performance)
- Storage: Micro-SD up to 256 GB

**Why this model?**
- USB interface (no special drivers for RPi)
- H.264 hardware encoding (lower bandwidth)
- 120° FoV captures bus lane + one adjacent lane (sufficient for violations)
- Vandal-resistant (industrial dashcam body)
- Cost-effective in volume

**Note:** Phase 1 adds interior camera for ride-sharing safety (requires second USB port or Ethernet camera)

---

### Vehicle Integration: OBD-II Dongle (ELM327)

**Part:** ELM327 OBD-II Bluetooth/USB dongle (generic, ISO9141-2 compatible)  
**Cost:** $35 USD  
**Specs:**
- Interface: USB (preferred over Bluetooth for reliability)
- Protocols: KWP2000, ISO9141-2, CAN 500K, CAN 250K
- Data: Real-time vehicle speed, RPM, fuel consumption, diagnostics
- Power: 5V from USB

**Why this model?**
- Standard OBD-II pinout (works on all 1996+ vehicles)
- USB preferred over Bluetooth (more reliable in cellular interference)
- Provides ground truth for speeding detection (Phase 1)
- Well-supported by Linux kernel (vcan, can-utils)

**Data Stream (for Phase 0):**
- Vehicle speed (speed validation for bus lane, red light context)
- Engine load (idle detection)
- Diagnostic codes (vehicle health)

---

### GPS Module: u-blox NEO-M9N

**Part:** u-blox NEO-M9N (breakout board with antenna)  
**Cost:** $28 USD  
**Specs:**
- Accuracy: 5 mm (typical, good for geofencing)
- Update Rate: 30 Hz (responsive to road curves)
- Interface: UART (3.3V, 9600 baud)
- Sensitivity: −167 dBm
- Time to First Fix: 30 sec (cold start)
- Antenna: Ceramic patch (integrated, weatherproof)

**Why this model?**
- u-blox is automotive-grade (tested to −40°C)
- 5 mm accuracy sufficient for 50m geofence dedup
- 30 Hz updates capture lane-change GPS drift
- UART interface standard on RPi (GPIO pins)

**Data Stream:**
- Latitude, longitude (incident location)
- Altitude (elevation changes on hills)
- Dilution of Precision (GPS confidence)

---

### LTE Modem: Quectel EC25

**Part:** Quectel EC25-E (Mini PCIe module, no antenna)  
**Cost:** $50 USD (+ $15 for antenna + $5 for USB adapter)  
**Total Modem Cost:** $70  
**Specs:**
- LTE: Cat-4 (up to 150 Mbps DL, 50 Mbps UL, sufficient for video upload)
- 2G/3G fallback (GSM, HSPA+)
- Interface: USB via Mini PCIe→USB adapter
- Antenna: External RP-SMA (5 dBi, rooftop-mounted)
- Power: 3.3V/5V (via USB)

**Why this model?**
- Supports Canada bands (LTE Band 7, 20, 32; 2G band 8, 5)
- Low power consumption (Phase 1+ enables 5G for faster uploads)
- Well-supported on Linux (usb_modeswitch, ModemManager)
- Industrial temperature range

**Bandwidth (Phase 0):**
- Evidence clip: ~100 MB @ 10 Mbps = 80 sec upload
- Metadata + heartbeat: <1 KB every 10 sec
- Fully async (buffered, non-blocking)

---

### Security: TPM 2.0 Module (SLB9670)

**Part:** Infineon OPTIGA™ Trust M TPM 2.0 (SLB9670, I²C)  
**Cost:** $18 USD  
**Specs:**
- Crypto: RSA-2048, ECDSA P-256, AES, SHA-256
- I²C Interface (3.3V)
- Secure key storage (non-extractable)
- Hardware RNG
- Attestation support

**Why this model?**
- **Phase 0 use:** Store HMAC-SHA256 node secret (read-only via sealed storage)
- **Phase 1 use:** Hardware attestation (prove node firmware integrity)
- **Phase 2+ use:** Smart contract wallet seeds, DID private keys
- Already audited for automotive applications

**Note:** Phase 0 uses simple shared secrets; TPM is reserved for Phase 1 firmware attestation.

---

### Power Management: OBD-II + Battery Backup

**OBD-II Power Adapter**  
**Part:** Generic OBD-II 12V→5V buck converter (10A rated)  
**Cost:** $25 USD  
**Specs:**
- Input: 12V (vehicle battery, 10–16V tolerance)
- Output: 5V @ 2.5A regulated
- Protection: Fused (15A automotive fuse)
- Efficiency: 85% (minimal heat)

**Battery Backup (UPS Hat)**  
**Part:** Generic 5V 10 Ah lithium battery pack (with PCM + boost circuit)  
**Cost:** $65 USD  
**Specs:**
- Capacity: 10 Ah @ 5V
- Chemistry: Li-ion 18650 (reliable, widely available)
- Runtime: 2–4 hours (RPi + modem only, camera disabled)
- Auto-switchover on main power loss
- Protection: Over-current, over-temperature

**Why this setup?**
- **Continuity:** Keeps node alive if vehicle power drops (e.g., ignition off, short disconnect)
- **Buffering:** Evidence clips queued locally during connectivity loss
- **Resilience:** Critical for enforcement (can't lose evidence mid-upload)

---

### Enclosure: IP67 Aluminum

**Part:** Generic aluminum DIN-rail enclosure (150 × 100 × 75 mm)  
**Cost:** $28 USD  
**Specs:**
- IP67 rating (dust + water jet proof)
- Material: 1.5 mm aluminum (thermal dissipation for modem/buck converter)
- Mounting: DIN rail inside, cable glands on base
- Heat dissipation: Passive (no fans, automotive reliability)
- Dimensions: Fits under bus seat or in wheel well

**Weatherproofing Add-ons:**
- Silicone gasket + stainless M4 hardware: $12
- Cable glands (M20, M16): $10

**Why this config?**
- Canadian buses exposed to −20°C winters, rain, salt spray (IP67 essential)
- Aluminum dissipates modem heat passively (LTE modem ~3W typical)
- DIN rail allows easy module replacement in field

---

### Cabling & Connectors

| Item | Cost | Notes |
|------|------|-------|
| OBD-II Y-harness (power + CAN) | $15 | Splits 12V (to buck) + CAN (unused Phase 0) |
| USB Type-C (RPi to modem) | $5 | Shielded, 3m length |
| Micro-USB (dashcam) | $3 | Redundant (both USB-C + micro available) |
| CAT6 Ethernet (for bench test) | $5 | Optional, helpful for field diagnostics |
| Terminal blocks + Anderson connectors | $8 | Reliable power distribution, quick disconnects |
| **Subtotal** | **$36** | |

---

### NFC Tag (Enrollment)

**Part:** NTAG216 (ISO14443A, 888 bytes)  
**Cost:** $2 USD  
**Data Stored:**
- Node ID: "OC_TRANSPO_BUS_001"
- Firmware version: "0.1.0"
- Certificate thumbprint: Blake3 hash
- **Read-only:** Tag locked after factory programming

**Why this?**
- Technician scans tag with smartphone to verify node during installation
- Tamper-evident: Locked tag ensures config integrity
- Phase 1+ supports W3C Verifiable Credentials (DID authentication)

---

### Firmware & OS

**Micro-SD Card (32 GB, U3, A2)**  
**Cost:** $8 USD  
**Contents:**
- Raspberry Pi OS Lite (−35 MB)
- Python 3.11 + dependencies (−200 MB)
- TensorFlow Lite models (−80 MB, ALPR + lane detection)
- Local incident buffer (remaining space)

**Firmware Image:**
- Pre-burned on 4 GB eMMC at factory
- SD card used as **read-only backup** (emergency boot fallback)

---

### Documentation & Chain of Custody

| Item | Cost | Notes |
|------|------|-------|
| Tamper-evident seals (5/node) | $15 | Hologram stickers, unique serial per node |
| QR code labels (model, serial, MAC) | $2 | Asset tracking, firmware version, MAC address |
| Installation manual (printed) | $1 | Quick-start guide for OC Transpo technician |
| Calibration certificate | $1 | GPS accuracy, camera lens distortion profile |
| **Subtotal** | **$19** | |

---

### Assembly & QA

**Assembly Labor:** 30 minutes @ $50/hr = **$25/unit**

**QA Checklist:**
1. Power delivery test (5V, 12V stable)
2. Camera stream test (USB enumeration, 30 fps capture)
3. GPS cold start (<60 sec lock)
4. OBD-II read (speed validation)
5. Cellular connectivity (LTE registration, data throughput >1 Mbps)
6. TPM secure key load (HMAC verify)
7. Temperature stress (−5°C to +45°C functional)
8. Enclosure seal test (hose spray, no water ingress)

**Time:** ~20 minutes per node

---

### Packaging & Shipping

**Per-Unit Packaging:** $8  
- Custom foam cutout (protective)
- Anti-static bag
- Thermal padding (winter shipping)
- Tracking label

**Bulk Shipping (50 units):**
- Pallet: 50 units @ 2 kg each = 100 kg
- Toronto → OC Transpo depot: ~$300 (freight)
- Cost per unit: $6

---

## Phase 0 Cost Summary

| Tier | Unit Cost | 50-Unit Batch | Notes |
|------|-----------|---------------|----|
| **COGS (Parts only)** | $420 | $21,000 | Hardware components |
| **Assembly + QA** | $25 | $1,250 | Labor, testing |
| **Packaging + Shipping** | $14 | $700 | Physical delivery |
| **Warranty Reserve (5%)** | $23 | $1,150 | Defect replacement |
| **Total Cost Per Unit** | **$482** | **$24,100** | Factory cost |
| **Markup (30% margin)** | +$145 | — | Gross profit |
| **Sales Price Per Node** | **$627** | — | OC Transpo unit cost |

---

## Procurement Strategy

### Phase 0 (5–10 units, lab/pilot)
- Direct purchase from distributors (RS, Digikey, Mouser)
- Lead time: 2–3 weeks
- Cost: $550–650/unit (retail)

### Phase 1 (50 units, full OC Transpo rollout)
- Contact manufacturers for volume pricing
- Lead time: 4–6 weeks
- Target cost: **$485–500/unit** (factory contract)
- Suppliers:
  - **Compute:** Raspberry Pi (direct partnership)
  - **Dashcam:** Viofo or OEM (bulk rebrand)
  - **Modem:** Quectel (authorized distributor, CAD inventory)
  - **Integration:** Contract assembly house in Ontario (30 min labor)

### Phase 2 (200+ units, city fleet expansion)
- Vertical integration (import components, in-house assembly)
- Target cost: **$420/unit**
- Explore shrink-wrapped kits (modular plug-and-play)

---

## Risk & Mitigation

| Risk | Impact | Mitigation |
|------|--------|-----------|
| **Component shortage** | 4–6 week delay | Multi-source key components (RPi, modem) |
| **Thermal stress** | Modem shutdown in summer | Active heatsink (Phase 1) or vehicle AC |
| **OBD-II compatibility** | Bus model-specific failures | Firmware fallback (use GPS + camera only) |
| **Battery degradation** | Data loss during long power-off | Replace battery every 2 years ($65) |
| **GPS urban canyon** | Lost signal in downtown tunnels | Pre-downloaded map (Phase 1+) |
| **Cellular connectivity** | Slow upload (Toronto coverage gaps) | Local buffering (2–4 hr storage) |

---

## Maintenance & Replacement

**Phase 0 (Pilot):** OC Transpo responsible for node maintenance (OC Transpo support includes 12-month warranty)

**Typical Lifespan:**
- **Compute (RPi CM4):** 5+ years
- **Camera:** 2–3 years (lens degradation, sensor burn-in)
- **Battery backup:** 2 years (Li-ion degradation)
- **Modem:** 3–4 years (firmware end-of-life)
- **Enclosure:** 10+ years (aluminum corrosion-resistant)

**Replacement Kit Cost:** ~$150 (camera + battery)

---

## References

- [CLAUDE.md § Hardware Stack](../CLAUDE.md#hardware)
- [PHASE_0_MVP_ARCHITECTURE.md § Edge Node Layer](./PHASE_0_MVP_ARCHITECTURE.md#edge-node-layer)
- [Raspberry Pi CM4 Datasheet](https://www.raspberrypi.com/products/compute-module-4/?variant=raspberry-pi-cm4000000)
- [u-blox NEO-M9N Integration Guide](https://www.u-blox.com/en/product/neo-m9n-module)
- [Quectel EC25 AT Command Manual](https://www.quectel.com/product/lte-ec25e/)

---

**Version:** 1.0  
**Last Updated:** 2026-06-10  
**Maintainer:** CivicMesh Hardware Team  
**License:** AGPL-3.0 (CivicMesh core)
