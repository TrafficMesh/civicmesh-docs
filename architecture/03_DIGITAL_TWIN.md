# Digital Twin Architecture

## Overview
The Digital Twin is a continuously updated, georeferenced model of Ottawa's road environment. It's not a static map like Google Maps, but a **living representation** that reflects what's happening on the road right now and what changed since yesterday.

Every node vehicle pass contributes fresh data:
- Road surface condition (cracks, heaving, potholes)
- Sign inventory and legibility
- Signal and pedestrian signal status
- Lane marking visibility (critical in winter snow coverage)
- Construction zone boundaries and equipment
- Pedestrian and cyclist activity (anonymized counts)
- Vegetation encroachment on sightlines
- Curb cut and accessibility infrastructure condition

---

## Why Digital Twin is a Platform Differentiator

### Coverage Guarantee
**Nexar's approach:** Crowdsourced civilian dashcams → coverage dense on arterials, sparse on residential streets. Coverage reflects where civilians happen to drive.

**CivicMesh approach:** Scheduled city vehicles on defined routes → coverage is systematic and guaranteed.
- Arterial streets: Updated multiple times daily (OC Transpo buses)
- Residential streets: Minimum once per plow cycle (winter) or sweep cycle (summer)
- Construction zones: Real-time updates during active deployment

Result: You can guarantee that every street in Ottawa gets a twin update at minimum once per plow season, guaranteed.

---

## Twin Data Pipeline

```
Node Vehicle Captures Frame + Metadata
       ↓
Edge AI Object Detection (signs, markings, hazards)
       ↓
Compressed metadata upload (300 bytes, not raw video)
       ↓
Server-side Fusion Engine
       ├─→ Geohash bucketing (assign to road segment)
       ├─→ Change detection (new observation vs. existing model)
       ├─→ Confidence update (prior confidence + new evidence)
       └─→ Timestamp tracking (when was this road last inspected?)
       ↓
Twin Update (asset condition rating, geometry changes)
       ↓
Notification Pipeline
       ├─→ Public Works (pothole → work order)
       ├─→ Emergency Management (signal outage)
       ├─→ Urban Planning (pedestrian count analytics)
       └─→ Insurance API (road condition context for underwriting)
```

---

## Asset Classes & Detection Models

### 1. Road Surface Condition
| Detection | Model | Data Points |
|-----------|-------|-------------|
| **Potholes** | Object detection (YOLO) + depth estimation | Location, size (cm), severity (cosmetic vs. danger) |
| **Heaving/Cracking** | Semantic segmentation | Location, extent (% of segment), pattern |
| **Worn pavement** | Texture analysis (dark areas from wear) | Location, severity rating |

**Collection:** Every frame analyzed; aggregated per 10m road segment.

**Output:** Quarterly "road condition heat map" → Public Works prioritizes repairs.

### 2. Sign Inventory & Condition
| Detection | Model | Data Points |
|-----------|-------|-------------|
| **Sign detection** | YOLO (trained on stop signs, speed limits, warnings) | Sign type, location, orientation |
| **Legibility** | OCR confidence + image quality metrics | Text readable (%), fading level, damage |
| **Missing signs** | Temporal: sign was here last month, gone now | Flag for 311 / public works |

**Collection:** Every frame. Aggregate by sign ID (deduplicate same sign from multiple vehicles).

**Output:** "Sign inventory report" → City asset database updated automatically. Maintenance crews notified of faded/damaged signs.

### 3. Traffic Signals & Status
| Detection | Model | Data Points |
|-----------|-------|-------------|
| **Signal detection** | Object detection (bounding box) | Location, pole type, mounting |
| **Signal state** | MobileNet classifier (red/yellow/green/burnt-out) | Current state, timestamp |
| **Damage** | Image quality + contrast | Glass cracked?, housing damaged? |

**Collection:** Every frame passing intersection.

**Output:** Real-time signal outage alerts → Emergency Management notified immediately. If signal is burnt-out and remains off for 2+ days, work order generated.

### 4. Lane Markings
| Detection | Model | Data Points |
|--------|-------|-----------|
| **Marking presence** | Semantic segmentation (lane pixels) | Visible (%), coverage (solid vs. dashed) |
| **Marking color** | Color classification | White, yellow (single vs. double) |
| **Visibility in conditions** | Seasonal tracking | Summer: fully visible. Winter: buried under snow (track % buried) |

**Collection:** Every frame.

**Output:** Digital twin records "lane visibility score" per road segment per day. Winter (Nov–Mar): track when lanes are buried, when plows uncover. Inform future snow route planning.

### 5. Construction Zones
| Detection | Model | Data Points |
|--------|-------|-----------|
| **Zone boundaries** | Geofence provided by contractor | Perimeter, active hours |
| **Active equipment** | YOLO detection (excavators, dump trucks) | Equipment type, location within zone |
| **Hazards** | Traffic cones, barriers, exposed holes | Detection + geolocation |
| **Worker presence** | Pedestrian detection (anonymized count) | # people on site, time-of-day |

**Collection:** Every frame captured by Tier 3 contractor kits.

**Output:** Real-time tracking of active construction for dynamic routing (GPS apps, city traffic management).

### 6. Accessibility Infrastructure
| Detection | Model | Data Points |
|--------|-------|-----------|
| **Curb cuts** | Object detection | Location, quality (smooth vs. broken) |
| **Accessible parking signs** | YOLO + location tracking | Presence, condition, frequency of violations |
| **Tactile warning strips** | Segmentation | Present at intersections, condition |
| **Crossing signals (accessible)** | Detection + state (working vs. broken) | Audible signal present (inferred from audio if available) |

**Collection:** Sidewalk-facing camera frames (optional, requires separate camera).

**Output:** Accessibility audit report (annual) → Parks & Recreation, Urban Accessibility Committee.

### 7. Pedestrian & Cyclist Activity (Anonymized Counts)
| Detection | Model | Data Points |
|--------|-------|-----------|
| **Pedestrian counts** | YOLOv5 person class | Count per frame, time-of-day, intersection |
| **Cyclist counts** | YOLO bicycle detection | Count per frame, direction, time-of-day |
| **Dwell times** | Temporal: track same person/bike across frames (no ID) | Time spent in intersection, indicating congestion |

**Privacy:** No person re-identification; counts are anonymized per intersection per hour.

**Collection:** Every frame.

**Output:** Origin-Destination (OD) matrix for urban planners. "Intersection A at 8 AM has 200 pedestrians; Intersection B has 50." Informs traffic signal timing, pedestrian infrastructure investment.

---

## Digital Twin Database Schema

### Core Tables (PostgreSQL + PostGIS)

```sql
-- Road segments (10m chunks with geometry)
CREATE TABLE road_segments (
    segment_id UUID PRIMARY KEY,
    geometry GEOMETRY(LINESTRING, 4326),
    segment_length FLOAT,
    posted_speed_limit INT,
    jurisdiction VARCHAR(50),
    updated_at TIMESTAMP
);

-- Condition observations (each observation of a segment)
CREATE TABLE segment_observations (
    obs_id UUID PRIMARY KEY,
    segment_id UUID REFERENCES road_segments,
    observation_type VARCHAR(50), -- 'surface', 'marking', 'signal', etc.
    confidence FLOAT (0-1),
    metadata JSONB, -- varies by type
    timestamp TIMESTAMP,
    node_id VARCHAR(50), -- which vehicle observed this
    image_hash VARCHAR(64) -- for dedup, not raw image
);

-- Asset inventory (signs, signals, curbs, etc.)
CREATE TABLE assets (
    asset_id UUID PRIMARY KEY,
    asset_type VARCHAR(50), -- 'stop_sign', 'speed_limit', 'signal', 'curb_cut'
    location GEOMETRY(POINT, 4326),
    geometry GEOMETRY(POLYGON, 4326), -- for bounding area
    asset_data JSONB, -- e.g., {"sign_text": "STOP", "facing": 180}
    last_observed TIMESTAMP,
    condition_rating INT (1-5), -- 1=poor, 5=excellent
    created_at TIMESTAMP,
    updated_at TIMESTAMP
);

-- Condition changes (for alerting & work order generation)
CREATE TABLE condition_changes (
    change_id UUID PRIMARY KEY,
    asset_id UUID REFERENCES assets,
    change_type VARCHAR(50), -- 'new_observation', 'degradation', 'missing'
    severity VARCHAR(50), -- 'critical', 'major', 'minor'
    description TEXT,
    created_at TIMESTAMP,
    routed_to_department VARCHAR(50), -- 'public_works', 'emergency_mgmt', etc.
    work_order_id VARCHAR(50) -- if generated
);

-- Temporal tracking (when was road last inspected?)
CREATE TABLE inspection_history (
    history_id UUID PRIMARY KEY,
    segment_id UUID REFERENCES road_segments,
    last_inspection TIMESTAMP,
    observation_count INT,
    confidence_score FLOAT, -- aggregate confidence for this segment
    next_inspection_due TIMESTAMP
);
```

### Indexing for Performance
```sql
-- Spatial: fast "what's within 100m of this point?"
CREATE INDEX idx_segment_geometry ON road_segments USING GIST(geometry);
CREATE INDEX idx_asset_location ON assets USING GIST(location);

-- Temporal: fast "what changed in last 24 hours?"
CREATE INDEX idx_changes_timestamp ON condition_changes(created_at DESC);
CREATE INDEX idx_observations_timestamp ON segment_observations(timestamp DESC);

-- Lookup by asset type
CREATE INDEX idx_assets_type ON assets(asset_type);
```

---

## Change Detection & Work Order Generation

### Alert Thresholds (Examples)

**Road Surface: Pothole Detected**
- Severity: size >20 cm → "Major" → Emergency alert to Public Works
- Severity: size 10–20 cm → "Moderate" → Scheduled work order
- Persistence: same pothole observed >3 times in 7 days → confirm real, escalate

**Signal: Burnt-Out Light**
- First observation: Flag and monitor
- After 24 hours: Still off → work order to Transportation Services
- After 7 days: Still off → escalate to emergency repairs

**Sign: Faded/Illegible**
- Legibility confidence <40% → Schedule maintenance crew for replacement

**Accessibility: Curb Cut Damaged**
- Damage detected + geotagged → Notify Parks & Recreation (AccessTO audit trail)

### Work Order Flow
```
Condition Change Detected
       ↓
Severity Assessment (algorithm + human review for major)
       ↓
Work Order Generation (auto-populated with location, asset ID, photos)
       ↓
Department Notification (email, API push to asset management system)
       ↓
Crew Dispatch & Completion Logging
       ↓
Twin Update (mark as resolved, record completion timestamp)
```

---

## Department-Specific Data Products

### Public Works (Road Maintenance)
**Data product:** Quarterly "Road Condition Index" heatmap

- Aggregate pothole/cracking/heaving observations per street segment
- Priority ranking: Which 20% of roads need immediate attention?
- Cost estimation: Based on damage extent, generate maintenance crew allocation
- Pre-winter: Condition assessment before snow season

**API:** REST endpoint `/api/v1/public-works/road-conditions?severity=major&district=Alta Vista`

### Emergency Management (Disaster Response)
**Data product:** Real-time signal outage / infrastructure damage map

- After major weather event, query twin for: Which signals are out? Which roads are blocked?
- Inform dispatch, emergency shelters, traffic management
- Real-time updates as plows clear roads (lane marking visibility improves)

**API:** WebSocket feed `/api/v1/emergency/infrastructure-status` (live updates)

### Urban Planning (Strategic Infrastructure Investment)
**Data product:** 3-year continuous performance analytics

- Pedestrian O/D matrices (where do people walk at what times?)
- Cyclist counting and route preference (which corridors are most used?)
- Intersection dwell times (indicator of congestion or unsafe design)
- Queue length trends (how long do vehicles wait at signals over time?)

**Use cases:**
- Decision support for Vision Zero (high-pedestrian intersections prioritized for safety upgrades)
- Bike lane expansion planning (ride count data shows where infrastructure is needed)
- Transit signal priority optimization (real data on which corridors have the most delays)

**API:** GraphQL endpoint (complex queries over historical data)

### OC Transpo (Transit Operations)
**Data product:** Real-time transit performance analytics

- Bus dwell times per route (time spent at each stop over time)
- Lane obstruction frequency per route (which corridors block buses most often?)
- Corridor speed profiles (average speed during peak vs. off-peak)
- Passenger count estimation (pedestrian detection near bus stops)

**Use case:** Dynamic routing optimization. If Rideau St. has chronic bus lane blocking, reroute or increase enforcement.

**API:** REST endpoint `/api/v1/oc-transpo/performance?route=1&day=Mon`

### Insurance API (Risk Assessment)
**Data product:** Road condition context for underwriting

- Violation density heatmaps (this intersection has 50 violations per month; high risk)
- Road condition index (smooth roads vs. hazardous surfaces; affects collision likelihood)
- Time-of-day risk profiles (speeding peaks at 3 AM on residential streets)

**Use case:** Premium pricing for high-risk corridors. Insurance company can offer discount if driver avoids high-violation areas during dangerous times.

**API:** `/api/v1/insurance/road-risk-assessment?lat=45.4&lon=-75.7&radius=1km`

---

## Continuous Improvement Loop

### Daily
1. Aggregate observations from all nodes (processed during night shift)
2. Update segment condition ratings (confidence scores accumulate)
3. Detect condition changes (pothole appeared, signal went out)
4. Route alerts to departments

### Weekly
1. Review false positives (signs detected that don't exist, phantom potholes)
2. Retrain object detection models on new field data
3. Publish public dashboard with updated infrastructure status

### Monthly
1. Publish "Road Condition Report" to council / public
2. Analyze trend data: Which roads are degrading fastest?
3. Compare pre/post maintenance: Did the repair work?

### Quarterly
1. Formal model evaluation: Precision, recall of asset detection
2. Update asset inventory from ground truth
3. Publish trend analysis for urban planning

---

## Privacy & Data Governance

### No PII in Twin
- Pedestrian/cyclist counts are anonymized (no person re-identification)
- Vehicle data is incident-specific; owner addresses are NOT stored
- Frames are deleted after metadata extraction (only metadata persists in twin)

### Retention Policy
- Observations: 3 years (statute of limitations for civil liability)
- Assets: Permanent (road infrastructure is permanent)
- Changes/alerts: 7 years (audit trail)

### Data Sharing
- Public: Road condition index (anonymized, neighborhood-level aggregation)
- City departments: Full resolution (geofence polygons, asset coordinates)
- Insurance API: Aggregate, anonymous (no individual vehicle or trip data)

---

## Technology Stack (Digital Twin)

- **Geospatial DB:** PostgreSQL + PostGIS (proven, open source)
- **Raster analysis:** GDAL / Rasterio (pothole/crack detection)
- **3D visualization:** Cesium.js (browser-based 3D map)
- **Time-series:** TimescaleDB extension (efficient temporal queries)
- **API:** GraphQL + REST (flexible querying)
- **Caching:** Redis (speed up hot queries, e.g., "what's in this geofence?")

---

## Future Twin Extensions

- **4D (spatial + temporal):** Animate road changes over time (e.g., "watch a pothole grow over 6 months")
- **Predictive degradation:** ML model predicts which roads will fail in next 12 months (inform preventive maintenance)
- **Simulation integration:** City planners run traffic simulations against actual twin data (not synthetic scenarios)
- **Multi-city federation:** Aggregate twins across Ontario cities (shared asset database, comparative analysis)
