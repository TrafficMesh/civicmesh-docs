# Hardware Stack & Bill of Materials

## Overview
Each node vehicle requires:
- OBD-II dongle for vehicle telemetry
- Forward-facing dashcam for road surface/vehicle capture
- Edge compute module (Raspberry Pi CM4 or equivalent)
- Cellular modem for encrypted evidence upload
- GPS module for incident geolocation
- Power management (OBD primary + battery backup)

**Total BOM cost:** ~$485–600 per unit (fully assembled)

---

## Component Specifications

### 1. Edge Compute Module
| Spec | Value | Notes |
|------|-------|-------|
| **Model** | Raspberry Pi CM4 (4GB RAM minimum) | Industrial temp range -20°C to +50°C |
| **CPU** | ARM Cortex-A72 (4-core) | 1.5 GHz sufficient for TFLite inference |
| **RAM** | 4GB minimum, 8GB recommended | Must fit inference models + OS |
| **Storage** | 32GB eMMC minimum | Sufficient for OS + edge models + local buffer |
| **Cost** | ~$50 | Bulk pricing available |

**Rationale:** Broad software ecosystem, industrial availability, cost-effective. Alternatives: Radxa RockPi, Khadas VIM3 (similar specs).

---

### 2. Dashcam
| Spec | Value | Notes |
|------|-------|-------|
| **Model** | Viofo A119 Mini or equivalent | 1440p/30fps recommended (not full 4K to conserve bandwidth) |
| **Resolution** | 1440p (2560×1440) | Sufficient plate reading at highway speed |
| **Frame Rate** | 30 fps minimum | Sufficient for motion detection; 60 fps if bandwidth allows |
| **FOV** | 160°+ wide angle | Captures full lane width + adjacent lanes |
| **Low-Light** | f/1.6 aperture or better | Ottawa winters, night ops critical |
| **Video Codec** | H.264 (MP4 container) | Standard, widely compatible, hardware acceleration on Pi |
| **Cost** | ~$200 | Quality matters for legal evidence |

**Interface:** USB or Ethernet (GigE PoE ideal but rare in dashcams). Use frame buffer + GStreamer pipeline to sync with metadata.

**Calibration:** Lens distortion correction required for accurate lane detection. Store calibration matrix on node.

---

### 3. OBD-II Interface
| Spec | Value | Notes |
|------|-------|-------|
| **Standard** | ELM327-compatible OBD-II dongle | STN1110 recommended (more reliable than clone ELM327) |
| **Protocol** | CAN, ISO9141, PWM, VPW | ELM327 handles all via serial |
| **Data Points** | Speed (MPH/KMH), RPM, heading (if GPS available), timestamp | Critical: speed anchors speeding violations |
| **Interface** | Serial (USB or UART) | USB models plug into Raspberry Pi |
| **Cost** | ~$30 | Avoid counterfeit chips (inconsistent performance) |

**Why OBD is critical:** Provides ground-truth vehicle speed (more reliable than GPS-based estimation), synchronized with dashcam capture.

**Polling:** Query at 10 Hz (every 100ms) during motion. Buffer locally; sync with video frames.

---

### 4. GPS + Antenna
| Spec | Value | Notes |
|------|-------|-------|
| **Model** | Ublox Neo-M8N or equivalent | Industrial-grade, -40°C to +85°C |
| **Accuracy** | ±5m (CEP95, good for urban) | Sufficient for geofence-based enforcement (school zones, construction) |
| **Update Rate** | 5 Hz (200ms fixes) | Sufficient to track road position; sync with video |
| **GNSS Constellation** | GPS + GLONASS + Galileo | Multi-constellation improves urban canyon performance |
| **Interface** | UART or USB | Serial connection to Raspberry Pi |
| **Cost** | ~$25 | Neo-M8N is proven, industrial-grade |

**Integration:** Sync GPS timestamp with video frame timestamps and OBD data to create unified incident record.

---

### 5. Cellular Modem
| Spec | Value | Notes |
|------|-------|-------|
| **Model** | Quectel EC25 (LTE Cat-4) | Industrial, -40°C to +80°C, small form factor |
| **Technology** | LTE Cat-4 (North America bands) | Sufficient for ~500 Kbps uplink (compressed evidence clips) |
| **Bands** | B2, B4, B5, B12, B13, B28 | Full North American coverage (Rogers, Telus, Bell, Videotron) |
| **Interface** | USB or mini-PCIe | USB models easier to integrate |
| **SIM** | Consumer LTE plan or IoT SIM | IoT SIMs ($5–15/mo) offer better data bulk pricing |
| **Cost** | ~$40 | Bulk data costs: ~$0.50–1.00 per GB (negotiate with carrier) |

**Data profile:** 
- Incident upload: ~500 KB per clip (compressed H.264 + metadata)
- 10 incidents/day = ~5 MB/day = 150 MB/month
- Budget ~500 MB/month per node for safety margin

**Fallback:** Vehicle returns to depot nightly → Wi-Fi offload of queued incidents (cost reduction).

---

### 6. Power Management

#### Primary: OBD Power
- OBD-II port provides 12V vehicle power (when engine running or ACC mode)
- Use buck converter (12V → 5V USB) to power Raspberry Pi + modem + GPS
- Efficiency: 85%+ at 2A draw

#### Backup: Battery
- **UPS module:** Supercap + LiPo (Li-ion single cell, 3.7V)
- **Capacity:** 5000 mAh (provides 8-hour backup at 0.5A draw)
- **Purpose:** Survive vehicle shutdown (parking lot, overnight); upload queued incidents when vehicle restarts
- **BMS:** Integrated charge management; auto-switch to LiPo if OBD power drops

#### Power Budget
| Component | Draw | Notes |
|-----------|------|-------|
| Raspberry Pi | 0.5–1.5 A | Varies with inference workload |
| Dashcam | 0.2–0.5 A | USB powered |
| Modem | 0.1–0.3 A | Idle <0.1A, transmit spikes to 0.8A |
| GPS | <0.05 A | Minimal draw |
| **Total** | ~1–2 A | Average 12V draw; peaks during upload |

**Thermal:** Heat dissipation critical in summer. Use aluminum enclosure with internal ventilation.

---

### 7. Weatherproof Enclosure
| Spec | Value | Notes |
|------|-------|-------|
| **Rating** | IP67 (dust + water immersion to 1m) | Mandatory for automotive outdoor use |
| **Material** | Aluminum + rubber gaskets | Withstand Ottawa winter -30°C, salt spray |
| **Mounting** | Magnetic roof mount or suction cup | Quick-deploy for Tier 3 contractor kits |
| **Cooling** | Passive ventilation (no fans) | Reduce moving parts, reliability |
| **Size** | ~10" × 6" × 4" (compact) | Fit under roof rack without aerodynamic impact |
| **Sealing** | Tamper-evident holographic seals (4×) + security fasteners (Torx-pin) | Serialized seals (on-chain) provide proof of tampering; specialized tools prevent casual disassembly |
| **Cost** | ~$80 | Custom mold investment justified by volume |

**Security features:**
- **Tamper-evident seals:** Holographic foil, serialized (e.g., TES-2026-001847-A), linked to nodeID on-chain. Broken seal = immediate evidence quarantine.
- **Security fasteners:** Torx-with-pin screws (require specialized driver, not standard Phillips/flathead). Prevents unauthorized access.
- **Cable sealant:** Marine-grade polyurethane at cable entries (not removable without cutting).
- **PCB potting:** Transparent epoxy encapsulation of main circuit board (prevents physical probing, side-channel attacks).

**Testing:** IP67 certification, thermal cycling (-30°C to +60°C), vibration per ISO 16750-3.

**See also:** [docs/FIELD_OPERATIONS.md Part 3](../FIELD_OPERATIONS.md) for full tamper-proof hardware architecture (5 layers: physical sealing, TPM identity, secure boot, intrusion detection, runtime heartbeat).

---

### 8. Trusted Platform Module (TPM 2.0)
| Spec | Value | Notes |
|------|-------|-------|
| **Chip** | Infineon SLB9670 (ISO/IEC 11889 compliant) | Permanently soldered, air-gap isolated from CPU |
| **Function** | Non-extractable cryptographic key storage | Private key cannot be read; can only be used for signing |
| **Interface** | I2C or SPI | Communication with Raspberry Pi kernel |
| **Certification** | Common Criteria EAL 4+ | Highest practical security certification |
| **Cost** | ~$5 | Factory integration |

**Purpose:** Every node has a unique public-private key pair. All telemetry (heartbeat, speed data, GPS) is signed by TPM. Backend verifies signature on every upload—if invalid, device is quarantined immediately (indicates TPM key compromise or unauthorized firmware).

**See also:** [docs/FIELD_OPERATIONS.md Part 3, Layer 2](../FIELD_OPERATIONS.md) for TPM-based device identity and signature verification.

---

### 9. Expansion Connector (Future Sensor Modules)
| Spec | Value | Notes |
|------|-------|-------|
| **Connector** | Molex MicroFit 3.0, IP67 overmold | Automotive-grade, weather-sealed |
| **Pins** | I2C (2) + SPI (4) + GPIO (4) + 5V power (2) | Supports multiple sensor types |
| **Location** | Side of aluminum enclosure | Accessible for technician maintenance |
| **Firmware** | Plugin architecture (signed ELF binaries) | Auto-detect plugins at boot, verify signatures via TPM |
| **Cost** | ~$8 | PCB routing + connector assembly |

**Purpose:** Phase 2+ upgrades can add sensors (air quality, noise, water detection, etc.) without replacing hardware. Plugins are community-developed (open source) or commercial (partner BSL), all signed and governed by DAO.

**See also:** [docs/FIELD_OPERATIONS.md Part 7](../FIELD_OPERATIONS.md) for sensor capabilities roadmap (air quality, noise, flood, pothole, streetlight detection).

---

## Node Tiers Summary

| Tier | Vehicles | Deployment | Hardware | Power |
|------|----------|------------|----------|-------|
| **T1: Permanent City Fleet** | OC Transpo buses, bylaw, police, Public Works | Hardwired, year-round | Full stack, ruggedized | OBD primary only |
| **T2: Seasonal City Fleet** | Snow plows, sweepers, line painters | November–March or summer | Full stack, ruggedized | OBD + battery |
| **T3: Contractor Module** | Road crews, event crews | Temporary per contract | Self-contained kit | Battery primary, OBD backup |
| **T4: Civilian Opt-In** | Consumer vehicles (future) | Voluntary, year-round | Consumer kit (simplified) | 12V cigarette lighter adapter |

---

## Assembly & Testing Workflow

1. **BOM sourcing:** Bulk order, negotiate with suppliers (Raspberry Pi, Quectel, Ublox)
2. **Assembly:** Pick-and-place + soldering (outsource or in-house assembly line)
3. **Firmware flash:** Custom Ubuntu image with pre-loaded software stack
4. **Calibration:**
   - Dashcam lens distortion correction
   - OBD polling sync
   - GPS datum check
   - Modem band selection (carrier-specific)
5. **Testing:**
   - IP67 certification (submersion test)
   - Thermal cycling (-30°C to +60°C, 10 cycles)
   - Vibration (vehicle mounting points)
   - RF interference (modem + GPS near metal roof)
6. **Quality assurance:** 100% pre-deployment burn-in (24 hours operation)
7. **Labeling:** Serial number, MAC address, firmware version, calibration date

---

## Security & Expansion Hardware (Added for Tamper-Proofing)

| Component | Cost | Purpose |
|-----------|------|---------|
| TPM 2.0 Chip (Infineon SLB9670) | $5 | Non-extractable device identity, cryptographic signing, secure boot |
| Chassis Intrusion Switch | $2 | Detect enclosure tampering (reed switch + magnet, GPIO-wired) |
| Tamper-Evident Seals (4× per unit) | $4 | Serialized holographic seals, physical proof of tampering |
| Security Fasteners (Torx-pin screws) | $3 | Prevent casual disassembly (specialized tool required) |
| Expansion Connector (I2C/SPI/GPIO) | $8 | Future sensor modules (air quality, noise, water detection) |

---

## Cost Breakdown (Per Unit)

| Component | Cost |
|-----------|------|
| Raspberry Pi CM4 | $50 |
| Dashcam | $200 |
| OBD-II dongle | $30 |
| GPS module | $25 |
| Cellular modem | $40 |
| Power management (buck, UPS, BMS) | $60 |
| Enclosure + mounting | $80 |
| **BOM subtotal (core hardware)** | **$485** |
| **Security & Expansion (new)** | **$22** |
| **BOM subtotal (with security)** | **$507** |
| Assembly labor | $50 |
| Testing + calibration | $25 |
| **Total landed cost (Tier 1)** | **~$582** |

**Bulk pricing (100+ units):** -10–15% discount expected.

**Tier 3 (Contractor kit):** Identical BOM; simplified enclosure (magnetic mount), reduces assembly complexity by ~5%.

---

## Recommended Suppliers (Canada)

- **Raspberry Pi:** Distrelec, Arrow Electronics (industrial distributors)
- **Dashcam:** Viofo (online direct), or RoboShop
- **Cellular modems:** Digi-Key, Mouser Electronics
- **OBD dongles:** Amazon or Aliexpress (commodity item)
- **Enclosure:** Custom mold (Shenzhen contract manufacturer, 500+ MOQ)

---

## Future Hardware Roadmap

- **Year 2:** V2 Dashcam (4K, dual-channel front+rear)
- **Year 3:** Integrated modem (LTE in Raspberry Pi, single module)
- **Year 4:** mmWave radar (following distance, collision risk)
- **Year 5:** Multi-spectral (IR for night, polarization for reflective surfaces)
