# OS-PATROL-TM-001 TrafficMesh Technical Architecture v1

**OS-PATROL-TM-001**
**D-CENTRAL GROUP**
**TrafficMesh Technologies Inc.**
TrafficMesh Technical Architecture


| Document ID | OS-PATROL-TM-001 |
|---|---|
| Version | 1.0 |
| Status | Draft — SR&ED primary |
| Date | May 2026 |
| Author | Toussaint Redji Jean Baptiste |
| Classification | Confidential — Technical |
| SR&ED Eligibility | Primary — edge AI, ALPR, OBD-II telemetry fusion, digital twin, chain-of-custody pipeline |
| Related Documents | DC-CM-ARCH-001 | OS-PATROL-TM-FLEET-001 | OS-PATROL-TM-TWIN-001 | OS-PATROL-TM-EV-001 | IRAP-TM-001 |




| Executive Summary
TrafficMesh is a distributed mobile traffic enforcement platform that turns existing city fleet vehicles into enforcement nodes. This document defines the complete technical architecture: edge compute stack, violation detection AI pipeline, ALPR integration, OBD-II telemetry fusion, LTE evidence upload, server-side ingestion and processing, officer review portal, and evidence storage. All components are open source (AGPL v3) with Commercial Edition MSSP orchestration layer. |
|---|


# 1. System Architecture Overview


| Component | Technology | SR&ED? | Key Challenge |
|---|---|---|---|
| Edge Compute Module | NVIDIA Jetson Orin NX 8GB, TensorRT inference runtime | ✅ Primary | Resource allocation between violation detection, ALPR, and privacy blurring models simultaneously |
| Violation Detection | YOLOv8 fine-tuned on Ottawa road footage + synthetic augmentation | ✅ Primary | Canadian weather/lighting/road conditions — no published benchmark exists |
| ALPR Engine | OpenALPR (community) fine-tuned on Canadian plates — all provinces | ✅ Primary | Plate recognition under motion blur, frost, snow occlusion at -30°C |
| OBD-II Interface | ELM327-compatible hardware adapter, ISO 15765-4 CAN protocol, Python-OBD library | ✅ Strong | GPS-OBD timestamp synchronisation and speed estimation from moving reference frame |
| GPS Module | u-blox ZED-F9P RTK, centimetre-accurate where RTCM correction available | ✅ Moderate | Accuracy degradation in urban canyon and LTE dropout scenarios |
| Evidence Pipeline | FastAPI ingestion API, Kafka queue, MinIO/S3 storage, PostgreSQL records | ✅ Moderate | Chain of custody atomicity under high concurrency |
| Officer Review Portal | React 18 frontend, FastAPI backend, GraphQL API, PostgreSQL | ✅ Partial | Human review UX design for high-volume enforcement workflows |
| Digital Twin Engine | PostGIS spatial database, custom update algorithm, Mapbox GL rendering | ✅ Strong | Continuous update from 200+ moving nodes with sub-24hr latency guarantee |


# 2. Violation Detection Pipeline
- Camera captures frame at 30fps → edge AI samples at 15fps (model inference rate)
- YOLOv8 violation candidate detection: outputs bounding boxes + class labels (violation type) + confidence scores
- If confidence ≥ 0.50 (pre-filter threshold): trigger ALPR model on frame → extract subject vehicle plate → hash non-subject plates
- Face detection model runs on frame → GaussianBlur applied to all detected faces
- OBD-II speed + GPS coordinates fetched with synchronised timestamp
- Evidence package assembled: frame + ALPR result + GPS + speed + violation type + confidence + timestamp
- ATECC608B signs evidence package with node private key → package queued for upload
- LTE upload to Ingestion API → schema validation → confidence routing (≥0.90 fast-track, 0.70–0.89 standard, <0.70 archived)
# 3. Violation Type Configuration


| Violation Type | Detection Method | Confidence Threshold | Legal Authority |
|---|---|---|---|
| Bus Stop Obstruction | YOLOv8 — detects vehicle in bus stop zone during bus approach. Zone defined by GPS geofence + visual bus stop sign detection. | 0.85 (fast-track) | Existing HTA s.170 + municipal bylaw — OC Transpo pilot scope |
| Bus Lane Obstruction | YOLOv8 + lane marking segmentation — detects non-exempt vehicle in designated bus lane | 0.80 | HTA s.154.1 — confirmed by legal opinion REG-TM-001 |
| School Zone Speed | OBD-II speed + GPS geofence (time-activated) — flags host vehicle speed OR optical flow estimate for other vehicles | 0.90 (requires dual confirmation) | HTA s.128 + municipal school zone designation |
| Snow Route Parking | ALPR cross-reference with city snow route activation schedule + GPS location. Detects parked vehicle on active snow route. | 0.95 (ALPR-driven) | Municipal bylaw — snow route parking prohibition |
| Accessible Parking Violation | ALPR + accessible permit database query + GPS parking zone overlay. Flags vehicles without valid permit in accessible spaces. | 0.90 | HTA s.27 + municipal bylaw |
| Expired/Suspended Plate | ALPR + MTO plate status query (API). Real-time plate status check. | 0.99 (ALPR-driven — very high) | HTA s.7 — goes to police queue, not bylaw queue |
| Construction Zone Speed | OBD-II speed + GPS geofence (contractor kit activated) — automatically activates 40km/h enforcement envelope around contractor location | 0.90 | HTA s.128 + posted construction zone speed |


# 4. OBD-II Telemetry Fusion


| Technical Challenge (SR&ED) | Estimating the speed of a target vehicle from a moving enforcement vehicle requires fusing: host vehicle speed (OBD-II), host vehicle GPS position and heading, target vehicle position from camera (bounding box centroid), optical flow velocity of target vehicle relative to camera frame, and camera-to-ground geometry. This is a novel sensor fusion problem with no published solution for this specific enforcement context. |
|---|---|
| Current Approach | Kalman filter fusing OBD-II speed, GPS velocity vector, and optical flow bounding box delta. Achieves 2.1 km/h RMS error vs reference in testing. Target: 1.5 km/h for enforcement use. Active SR&ED investigation. |
| Speed Evidence Limitation | Until RMS error target is achieved, speed evidence from TrafficMesh is advisory only — used to flag potential speed violations for officer review, not as the sole basis for citation. Speed citations require confirmation by the officer that the speed estimate is corroborated by additional context in the clip. |
| GPS Timestamp Synchronisation | OBD-II polling occurs asynchronously with camera frame capture. Timestamp fusion uses GPS pulse-per-second (PPS) signal as the authoritative time reference for all sensor data. Any OBD-II reading without a GPS-anchor within 100ms is flagged as unsynchronised and excluded from evidence packages. |


# 5. Technical Uncertainty Register
- Speed estimation: Kalman filter at 2.1 km/h RMS vs 1.5 km/h target — convergence approach under investigation;
- ALPR accuracy: current 0.78 F1 on Ottawa winter dataset vs 0.85 production target — preprocessing filter investigation in progress;
- Violation detection frame rate: 18fps current vs 25fps target on Jetson Orin NX with full privacy pipeline active;
- Digital twin update latency: currently 87ms median — within 100ms SLA but investigating consistency under high-concurrency node updates;
- Evidence pipeline atomicity: consent gate v3 approach tested to 500 concurrent requests — 1,000+ concurrency testing pending.
# 6. Document Control


| Ver | Date | Author | Description |
|---|---|---|---|
| 1.0 | May 2026 | Toussaint Redji Jean Baptiste | Initial release |




| Classification Notice — This document is CONFIDENTIAL and intended for internal use, legal counsel, and authorized personnel only. Distribution requires written authorization from Toussaint Redji Jean Baptiste. |
|---|

