# Phase 0 Dashcam Specifications & Calibration

**Camera selection, lens calibration, and integration guide for bus violation detection**

Version: 1.0  
Last Updated: 2026-06-10

---

## Quick Specs

| Parameter | Spec | Notes |
|-----------|------|-------|
| **Resolution** | 1440p (2560 × 1440) @ 30 fps | 4:3 aspect ratio, horizontal dominance |
| **Field of View (FoV)** | 120° horizontal | Captures bus lane + 1 adjacent lane |
| **Codec** | H.264 (hardware accelerated) | 8–10 Mbps bitrate (LTE friendly) |
| **Interface** | USB 2.0 | Plug-and-play, no custom drivers |
| **Lens Distortion** | <10% barrel | Corrected via software (Phase 1+) |
| **Low-Light Performance** | f/1.8 aperture | Functional at 500+ lux (dawn/dusk) |
| **Operating Temp** | −5°C to +50°C | Suitable for Canadian winters + summer |

---

## Camera Mounting

### Position: Front-Mounted (Bus Windshield)

**Placement:**
- **Height:** 1.2–1.5m from ground (typical bus windshield height)
- **Angle:** 0° (level with horizon, slight downward 5°−10° for lane markings)
- **Horizontal Offset:** Center of bus front (captures both lanes)
- **Distance from obstacles:** ≥30cm from wipers, ≥10cm from roof overhang

**Why front-mounted?**
- Captures bus lane violations (obstruction, red light running)
- Lane detection models trained on forward-facing perspective
- Minimal wind noise (mounted behind laminate)

### Mounting Hardware

```
┌──────────────────────────┐
│    Bus Windshield        │
│   (laminated glass)      │
├──────────────────────────┤
│      Adhesive Mount      │  ← 3M VHB 4910 (automotive-grade)
│      (camera bracket)    │
├──────────────────────────┤
│   USB Dashcam (1440p)    │  ← Weatherproof housing
│   with antenna opening   │  ← Allows cellular antenna view
└──────────────────────────┘
```

**Mounting Materials:**
- **Adhesive:** 3M VHB 4910 tape (automotive, −40°C to +60°C rated)
- **Bracket:** Aluminum with silicone vibration dampers (reduces road noise)
- **Cable routing:** Through weatherstrip (sealed with silicone)

---

## Lens Calibration

### Distortion Correction

**Problem:** Wide-angle lens (120° FoV) introduces barrel distortion (~8–10% at frame edges), affecting vehicle detection accuracy.

**Phase 0 Approach:** Pre-calibration profile stored in firmware

**Calibration Procedure:**
1. Photograph checkerboard pattern (8×6) at 2m distance
2. Use OpenCV `calibrateCamera()` to compute:
   - Focal length (fx, fy)
   - Principal point (cx, cy)
   - Distortion coefficients (k1, k2, k3)

**Sample Calibration Matrix:**
```
Camera Matrix (K):
┌                    ┐
│ fx   0  cx         │  fx ≈ 2800 (pixels)
│  0  fy  cy         │  fy ≈ 2800 (pixels)
│  0   0   1         │  cx ≈ 1280, cy ≈ 720
└                    ┘

Distortion Coefficients:
k1 = −0.08,  k2 = 0.12,  k3 = 0.0  (barrel: negative k1)
```

**Storage:** Calibration matrix burned into firmware at factory. Applied during frame capture (Phase 1+, optional for Phase 0).

---

### Geospatial Calibration (Extrinsics)

**Objective:** Map pixel coordinates in camera frame to real-world 3D locations.

**Camera Height:** 1.3m above road surface  
**Camera Angle:** −5° pitch (slight downward look)  
**GPS Reference:** Measured from bus GPS (u-blox NEO-M9N)

**Extrinsic Matrix (simplified):**
```
Camera → World Transform:
- Translation: (0, 1.3, 0) m relative to bus center
- Rotation: Pitch −5°, Yaw 0°, Roll 0°

PerspectiveTransform (for bird's-eye view reconstruction):
Map frame corners to road coordinates:
  (0, 720)   → (−3.5, 2.0) m  [left edge, near]
  (2560, 720) → (+3.5, 2.0) m  [right edge, near]
  (0, 0)      → (−2.5, 15)  m  [left edge, far]
  (2560, 0)   → (+2.5, 15)   m  [right edge, far]
```

**Use Case (Phase 1+):** Measure vehicle position, lane occupancy, stopping distance

---

## Image Processing Pipeline

### On-Device (Raspberry Pi)

**Frame Capture (30 fps):**
```python
1. USB camera → raw YUV420
2. Debayer + demosaic (if Bayer pattern)
3. Lens distortion correction (lookup table)
4. Crop to 16:9 (optional, save bandwidth)
5. H.264 encode (hardware-accelerated, 10 Mbps target)
6. Write to circular buffer (5 sec = 60 MB ring)
```

**Inference on Key Frames:**
- **Every 10th frame** (3 fps) → run TensorFlow Lite models
- Lane detection (semantic segmentation)
- Traffic light detection (YOLO object detection)
- Vehicle detection (YOLO)
- **GPU:** None (CPU-only on RPi, but models quantized)

### Backend (Optional Phase 1+)

**Re-encoding + Format Conversion:**
- Convert H.264 → MP4 (FFmpeg)
- Thumbnail generation (6 frames per minute)
- Metadata injection (GPS, speed, timestamp)

---

## Sensor Performance Specs

### Dynamic Range
- **Minimum:** 500 lux (dawn/dusk, overcast)
- **Maximum:** 100,000 lux (midday sun, reflected light)
- **Functional Range:** 500–50,000 lux
- **Performance:** Automatic white balance + gain correction

**Note:** Far exceeds typical dashcam (480–5,000 lux functional)

### Frame Rate
- **Capture:** 30 fps (hardware native)
- **Inference:** 3 fps (every 10th frame, CPU budget)
- **Playback:** 24 fps (smooth review for officers)

### Latency
- **Capture to buffer:** <33 ms (1 frame)
- **Detection to alert:** <500 ms (5 frames)
- **Alert to upload trigger:** <1 sec (queued for LTE)

---

## Environmental Specifications

### Operating Conditions

| Parameter | Spec | Notes |
|-----------|------|-------|
| **Temperature** | −5°C to +50°C | Canadian winter (−20°C with heated enclosure) |
| **Humidity** | 20–80% RH | Silica gel packets in enclosure |
| **Vibration** | MIL-STD-810H (bus suspension) | Mounted on dampers |
| **Shock** | 20G peak (pothole bump) | Aluminum bracket absorbs |
| **Salt Spray** | ASTM B117 (500 hr) | Outdoor exposure in winter salt |
| **Dust** | IP67 rating | Sealed enclosure protects sensor |

### Weatherproofing

**Lens Housing:**
- Polycarbonate dome (hydrophobic coating, prevents fogging)
- Internal heater element (−10°C, prevents condensation)
- Drain holes (weep holes for moisture escape)

**Connector Protection:**
- Grommet-sealed USB port (microfiber gasket)
- Conformal coating on PCB (salt spray resistance)

---

## Frame Format & Compression

### Raw Frame Specs

| Property | Value |
|----------|-------|
| **Resolution** | 2560 × 1440 (4:3) |
| **Bit Depth** | 8-bit per channel (Y'CbCr) |
| **Color Space** | ITU BT.601 (NTSC) |
| **Uncompressed Size** | ~11.2 MB per frame |

### H.264 Encoding

**Codec Settings (Hardware, RPi VideoCore IV):**
- **Bitrate:** 8–10 Mbps (adaptive, QoS-aware)
- **GOP (Group of Pictures):** 30 frames (1 sec keyframe interval)
- **Profile:** Main (not High, to save CPU)
- **Level:** 4.1 (supports 1440p @ 30 fps)

**Compression Ratio:** ~1100:1 (from uncompressed 11.2 MB/frame to 0.01 MB)

**Example Clips:**
- 30-second clip: ~3.75 MB (LTE upload: <5 sec @ 10 Mbps)
- 2-minute clip: ~150 MB (Phase 2+ evidence storage)

---

## Calibration Workflow

### Factory Calibration

1. **Intrinsic Calibration (Distortion):**
   ```bash
   # OpenCV checkerboard capture
   python calibrate_intrinsics.py --images=checkerboard/*.jpg --output=K.yaml
   ```
   - Produces: Focal length, principal point, distortion coefficients
   - Stored in: Firmware blob (non-volatile)

2. **Extrinsic Calibration (Position/Angle):**
   ```bash
   # Measure: Camera height (1.3m), pitch (−5°), yaw (0°)
   # Compute: Perspective transform matrix for bird's-eye view
   # Validation: Test with known lane width (3.0m standard), verify pixel-to-meter conversion
   ```

3. **Functional Test:**
   - Capture test footage (lane markings, traffic signals, vehicles)
   - Run inference (lane detection, traffic light detection)
   - Compare detected lanes vs. ground truth (manual measurement)
   - Accept: <5% deviation in lane boundaries

### Field Verification (Per Bus)

**OC Transpo Technician Checklist:**
- [ ] Camera mounted level (use smartphone level app, ±2°)
- [ ] No obstructions (wipers, roof lip, antennas)
- [ ] USB cable secured (velcro, no strain)
- [ ] Lens clean (isopropyl alcohol, microfiber cloth)
- [ ] Firmware version matches bundle (QR scan)
- [ ] Test clip uploaded (verify H.264 playback in web portal)

---

## Known Limitations & Workarounds

### Issue: Backlighting (Sun in Frame)

**Symptom:** Lane detection fails when sun is behind vehicle (halo effect)

**Mitigation:**
- Automatic brightness correction (histogram equalization)
- Fall back to GPS + OBD speed (no visual verification)
- Phase 1: IR filter + multi-spectral sensors

### Issue: Nighttime Performance

**Symptom:** Low lux (<500) causes high noise, poor lane visibility

**Mitigation:**
- Infrared LED ring (Phase 1+ option, +$20)
- Rely on street lighting (most urban routes suffice)
- Disable visual lane detection at night (use GPS trajectory only)

### Issue: Raindrops on Lens

**Symptom:** Water spots obscure lane markings

**Mitigation:**
- Hydrophobic lens coating (maintained quarterly)
- Wiper-like mechanism (Phase 1+, mechanical or heated)
- Accept reduced confidence during heavy rain (human review required)

### Issue: Lens Distortion at Edges

**Symptom:** Lane detection model trained on "normal" lenses fails at extreme FoV

**Mitigation:**
- Crop to 90° effective FoV (Phase 1)
- Retrain models on wide-angle datasets (currently done)
- Apply undistortion pre-processing (CPU budget allows)

---

## Replacement & Maintenance

### Lifespan
- **Design Life:** 3 years (industry standard for automotive dashcams)
- **Common Failures:**
  - Sensor: CCD/CMOS degradation (image quality drop)
  - Lens: Fungal growth (humidity), scratches, coatings fade
  - Electronics: Capacitor dry-out (−20°C thermal stress)

### Replacement Cost
- **New Camera Unit:** ~$180
- **Labor (install + calibration):** ~$30
- **Total:** ~$210 per replacement

### Preventive Maintenance
- **Monthly:** Wipe lens with microfiber cloth
- **Quarterly:** Replace silica gel packets (in enclosure)
- **Yearly:** Inspect for condensation, replace if present

---

## Testing & Validation

### Pre-Deployment Checklist

| Test | Pass Criteria | Method |
|------|---------------|--------|
| **Optical** | Capture readable lane markings @ 20m | Test footage review |
| **Encoding** | H.264 playback without artifacts | FFmpeg compliance check |
| **Thermal** | Enclosure <50°C in direct sun (1 hr) | IR thermometer |
| **Connector** | USB enumeration on cold-start | Plug/unplug 5× |
| **Vibration** | No image blur @ 40 mph over pothole | Frame inspection |
| **Weatherproofing** | No internal moisture after rain | Humidity sensor |

### Field Validation (Phase 0 Pilot)

- **50 buses × 4 weeks** = real-world footage corpus
- Validate lane detection accuracy (compare to manual review)
- Collect false negative cases (missed violations)
- Inform Phase 1 model retraining

---

## References

- [PHASE_0_MVP_ARCHITECTURE.md § Camera Capture Pipeline](./PHASE_0_MVP_ARCHITECTURE.md#edge-node-layer)
- [OpenCV Camera Calibration](https://docs.opencv.org/4.8.0/dc/d8b/tutorial_camera_calibration.html)
- [USB Video Class (UVC) Specification](https://www.usb.org/document-library/video-class-v1-5-specification)
- [ITU-R BT.601 Color Space](https://en.wikipedia.org/wiki/YCbCr#ITU-R_BT.601)

---

**Version:** 1.0  
**Last Updated:** 2026-06-10  
**Maintainer:** CivicMesh Hardware Team
