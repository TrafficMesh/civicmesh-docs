# Amber Alert & Wanted Vehicle System (Corrected Architecture)

**Distributed detection system with local ALPR matching, database syncing, and retroactive search.**

---

## System Overview

```
Police Database (CPIC/OPP)
├─ Amber Alerts
├─ Stolen vehicles
├─ Wanted persons (vehicles)
└─ Armed & dangerous (vehicles)
       ↓ (Real-time feed)
CivicMesh Platform
├─ Cache wanted plates locally on each vehicle
├─ Vehicles match ALPR scans against local database
├─ On match: Send evidence package to police
├─ Retroactive: Query past scans from all vehicles
└─ Police dashboard: Real-time operator view + telematics
```

This is NOT cloud-dependent for matching. Vehicles do local ALPR checking.

---

## Architecture Deep Dive

### Layer 1: Police Issue Alert

```
Scenario: Child abducted in blue Honda CR-V, ABC1234

Police (OPP):
  1. Create incident in system
  2. Enter details:
     {
       "alert_type": "AMBER_ALERT",
       "plate": "ABC1234",
       "province": "ON",
       "vehicle_description": "Blue Honda CR-V",
       "child_name": "Emma Smith",
       "age": 7,
       "photo_url": "...",
       "priority": "CRITICAL",
       "timestamp": "2024-06-04 14:30:00"
     }
  3. Push to CivicMesh API
```

### Layer 2: Database Sync to Fleet

```
CivicMesh Cloud:
  Receives alert from police API
    ↓
  Updates central wanted database:
    • Amber alerts (top priority)
    • Stolen vehicles (high priority)
    • Wanted persons - vehicles (medium)
    • Commercial vehicle violations (lower)
    ↓
  Broadcasts update to ALL CivicMesh vehicles:

Each Vehicle Edge Device (Jetson Orin):
  Receives sync message
    ↓
  Updates LOCAL wanted plates cache:
    plates_wanted = {
      "ABC1234_ON": {
        "type": "AMBER_ALERT",
        "priority": "CRITICAL",
        "timestamp": "2024-06-04 14:30:00",
        "description": "Blue Honda CR-V - missing child",
        "child_name": "Emma Smith"
      },
      ... (other wanted plates)
    }
    ↓
  Database sync frequency: Real-time (LTE/cellular)
  Local cache size: ~2000 plates (minimal footprint)
  Offline capability: Works with cached database
```

### Layer 3: Real-Time ALPR Matching

```
CivicMesh Vehicle #456 (Bus) on street:

Continuous ALPR scanning (30fps):
  1. Camera captures vehicle
  2. ALPR engine reads plate: "ABC1234"
  3. LOCAL lookup: Is ABC1234 in wanted database?
     ↓
     YES → MATCH FOUND!
     ↓
  4. Compile evidence package:
     {
       "match_time": "2024-06-04 14:51:20",
       "matched_plate": "ABC1234",
       "alert_type": "AMBER_ALERT",
       "vehicle_location": [43.6629, -79.3960],
       "vehicle_heading": 185,
       "vehicle_speed_obd": 45,
       "vehicle_speed_radar": 48,
       "alpr_confidence": 0.98,
       "dashcam_clip": (5-10 sec H.264 video),
       "all_telematics": {
         "obd": { speed, rpm, fuel, throttle },
         "gps": { lat, lon, heading },
         "radar": { front_distance, rear_distance, objects },
         "imu": { acceleration, steering_angle },
         "timestamp": "2024-06-04 14:51:20"
       },
       "vehicle_id": "BUS_456",
       "device_gps": [43.6629, -79.3960]
     }
     ↓
  5. IMMEDIATELY notify police:
     - Send evidence package
     - Include live dashcam feed
     - Send ALL telematics data
     - Alert nearby police units via dispatch
     ↓
  6. Enable live streaming:
     - Start streaming dashcam to cloud
     - Police can monitor in real-time
     - Public notifications start
```

**Key advantage:** Local ALPR matching = <500ms response, no cloud latency!

### Layer 4: Police Response Dashboard

```
┌─ CivicMesh Police Dashboard ─────────────────────────────────┐
│                                                               │
│ AMBER ALERT MATCH: ABC1234 (Blue CR-V)                       │
│ Match time: 2024-06-04 14:51:20                              │
│ Detection vehicle: Bus #456                                  │
│ Confidence: 98%                                              │
│                                                               │
│ ┌─ Operator Information ──────────────────────────────────┐  │
│ │ Real-time Location:                                      │  │
│ │   [Map showing bus #456 position]                        │  │
│ │   Address: King St @ York St, Toronto                    │  │
│ │   Heading: 185° (South)                                  │  │
│ │   Speed (OBD): 45 km/h                                   │  │
│ │   Speed (Radar): 48 km/h ±2                              │  │
│ │                                                          │  │
│ │ Vehicle Telematics:                                      │  │
│ │   RPM: 1800                                              │  │
│ │   Throttle: 35%                                          │  │
│ │   Engine Temp: 85°C                                      │  │
│ │   Fuel Level: 65%                                        │  │
│ │                                                          │  │
│ │ Acceleration/Braking:                                    │  │
│ │   Current Accel: 0.2 m/s² (normal driving)              │  │
│ │   Steering Angle: 2° (straight)                          │  │
│ │   Braking: No (coasting)                                 │  │
│ └──────────────────────────────────────────────────────────┘  │
│                                                               │
│ ┌─ Video Stream ───────────────────────────────────────────┐  │
│ │ [Live dashcam feed - real-time video]                    │  │
│ │ 🔴 STREAMING - Police only view                          │  │
│ │                                                          │  │
│ │ [Watch] [Record] [Screenshot] [Export]                  │  │
│ └──────────────────────────────────────────────────────────┘  │
│                                                               │
│ ┌─ Dispatch Actions ───────────────────────────────────────┐  │
│ │ [Send to nearest unit] [Dispatch helicopter]             │  │
│ │ [Enable live streaming to public] [Media alert]          │  │
│ │ [Query past scans] (retroactive search)                  │  │
│ │ [Record all telematics] (evidence preservation)          │  │
│ └──────────────────────────────────────────────────────────┘  │
│                                                               │
│ ┌─ Evidence Package ───────────────────────────────────────┐  │
│ │ • Dashcam video (5-10 sec clip, HD)                      │  │
│ │ • Full telematics (OBD, radar, IMU, GPS)                 │  │
│ │ • ALPR confidence: 98%                                   │  │
│ │ • Time stamped & signed (chain of custody)               │  │
│ │ • Ready for prosecution                                  │  │
│ │                                                          │  │
│ │ [Download Evidence] [Send to prosecutor]                 │  │
│ └──────────────────────────────────────────────────────────┘  │
│                                                               │
└───────────────────────────────────────────────────────────────┘
```

### Layer 5: Retroactive Search

```
Police: "Have we seen this plate before?"
  ↓
CivicMesh retroactive query:

GET /api/police/v1/plate-history
{
  "plate": "ABC1234",
  "province": "ON",
  "lookback_hours": 24,
  "all_vehicles": true  // Query all vehicles in fleet
}

Response:
{
  "matches": [
    {
      "vehicle_id": "BUS_456",
      "timestamp": "2024-06-04 14:51:20",  // Current match
      "location": [43.6629, -79.3960],
      "speed": 45,
      "confidence": 0.98,
      "has_video": true
    },
    {
      "vehicle_id": "BUS_123",
      "timestamp": "2024-06-04 10:30:15",  // 4+ hours ago
      "location": [43.6500, -79.3500],
      "speed": 52,
      "confidence": 0.96,
      "has_video": true
    },
    {
      "vehicle_id": "VAN_789",
      "timestamp": "2024-06-03 22:15:00",  // Yesterday evening
      "location": [43.6750, -79.3600],
      "speed": 48,
      "confidence": 0.97,
      "has_video": true
    }
  ],
  "total_matches": 3,
  "time_span": "24 hours",
  "geographic_coverage": "Downtown Toronto + Highway corridors"
}

Police can now:
  1. See vehicle's COMPLETE movement history
  2. Extract video clips from ALL matching scans
  3. Understand vehicle's route & pattern
  4. Predict next location
  5. Gather evidence from multiple angles/times
```

### Layer 6: Send Evidence Packages to Vehicles

```
Police request: "Send me video from those past matches"
  ↓
CivicMesh compiles evidence packages:

For each historical match:
  {
    "timestamp": "2024-06-04 10:30:15",
    "vehicle_id": "BUS_123",
    "location": [43.6500, -79.3500],
    "dashcam_video": (5-10 sec clip),
    "telematics": {
      "obd": { speed, rpm, heading, ... },
      "radar": { objects, distances, ... },
      "imu": { acceleration, steering, ... },
      "gps": { lat, lon, timestamp }
    },
    "alpr_data": {
      "plate": "ABC1234",
      "confidence": 0.96,
      "vehicle_model": "Honda CR-V",
      "color": "Blue"
    }
  }

These packages sent to:
  1. Police dashboard (viewing)
  2. Prosecutor (court evidence)
  3. Public (optional - if media release approved)
  4. Live streaming (if Amber Alert still active)
```

---

## Complete Data Flow Example

### Timeline: Amber Alert to Recovery

```
14:30:00  Child abducted
          Police issue alert with plate ABC1234
          
14:30:05  Alert pushed to CivicMesh cloud
          
14:30:10  All 250 CivicMesh vehicles sync
          Downloaded ABC1234 to local wanted database
          
14:30:15  Vehicle #456 (bus) on King St
          ALPR scans random vehicle → Not a match
          ALPR scans random vehicle → Not a match
          ...
          
14:51:20  Vehicle #456 scans plate ABC1234
          ┌─ LOCAL MATCH CHECK ─────────────────┐
          │ Is ABC1234 in wanted database?       │
          │ YES! AMBER ALERT                     │
          │ Confidence: 98%                      │
          └─ IMMEDIATELY (< 500ms) ─────────────┘
          
14:51:22  Evidence package compiled:
          • Dashcam clip (5-10 sec)
          • All telematics (OBD, radar, IMU, GPS)
          • Location: King St @ York St
          • Speed: 45 km/h (OBD), 48 km/h (radar)
          • Vehicle heading: 185° (south)
          
14:51:23  Sent to police dashboard
          Police officer sees:
          • LIVE dashcam of vehicle
          • Vehicle location on map
          • Real-time telematics
          • Evidence package ready
          
14:51:25  Live stream started to public
          50K notifications sent
          4,200 viewers within 10 minutes
          
14:51:30  Police dispatch ordered
          Nearest units redirected to King & York
          
14:52:00  Police officers in pursuit
          Monitoring live dashcam + telematics
          Vehicle speed: 45 km/h (OBD)
          Vehicle heading: Currently heading west
          Nearest unit ETA: 2 minutes
          
14:53:30  Vehicle spotted by police
          Visual confirmation: Blue CR-V matches
          Heading west on Bloor St (from telematics)
          
14:54:00  Vehicle intercepted
          Child found safe
          Abductor apprehended
          
TOTAL TIME: 24 minutes (from alert to recovery)
```

---

## Architecture Components

### 1. Police Database & Integration

```
Police Systems:
├─ CPIC (Canadian Police Information Centre)
├─ OPP (Ontario Provincial Police) systems
├─ Municipal police databases (Toronto, Ottawa, etc.)
└─ Amber Alert coordinator (provincial)

CivicMesh Integration:
├─ OAuth2 API (secure authentication)
├─ Real-time feed (wanted plates updates)
├─ Polling frequency: Every 5 minutes (configurable)
├─ Latency: <1 second delivery to vehicles
└─ Retry logic (handles network issues)
```

### 2. Vehicle Edge Cache

```
Each Vehicle (Jetson Orin):
├─ Local database of wanted plates
│  ├─ Amber alerts (priority: CRITICAL)
│  ├─ Stolen vehicles (priority: HIGH)
│  ├─ Wanted persons - vehicles (priority: MEDIUM)
│  └─ Commercial violations (priority: LOW)
│
├─ Update mechanism:
│  ├─ Real-time push (when new alert issued)
│  ├─ Polling (every 5 min fallback)
│  └─ Offline cache (works without connection)
│
├─ Storage:
│  ├─ SQLite database (lightweight, fast)
│  ├─ Typical size: ~2000 plates + metadata
│  ├─ Lookup time: <10ms (instant match)
│  └─ Memory footprint: <50MB
│
└─ Performance:
   ├─ ALPR scan: 30fps continuous
   ├─ Plate check: <1ms per scan
   ├─ Match notification: <500ms total
   └─ No cloud latency in matching
```

### 3. ALPR Matching Engine

```
Real-time ALPR Processing:

Camera frame (30fps)
  ↓
ALPR detection (TFLite model)
  ├─ Detect plate region
  ├─ Read characters
  ├─ Confidence score (80-99%)
  ↓
LOCAL database lookup:
  plates_wanted = {
    "ABC1234_ON": { alert_type: "AMBER_ALERT", ... },
    ...
  }
  
  Is plate in wanted database?
  ├─ YES → Match found
  └─ NO → Continue scanning
  ↓
On match:
  ├─ Compile evidence package
  ├─ Send to police (immediately)
  ├─ Send to cloud (backup)
  └─ Enable live streaming
```

### 4. Evidence Package Compilation

```
On ALPR match detected:

Immediately capture:
  ├─ Dashcam frame (high-resolution)
  ├─ 5-10 second video clip (H.264, encrypted)
  ├─ GPS location [lat, lon]
  ├─ Timestamp (synchronized to cloud)
  
OBD Telemetry (current reading):
  ├─ Speed (km/h)
  ├─ RPM
  ├─ Heading (compass)
  ├─ Throttle (%)
  ├─ Engine temp
  ├─ Fuel level
  └─ DTC codes (diagnostic)
  
Radar Data (current scan):
  ├─ Front radar objects (distance, velocity, angle)
  ├─ Rear radar objects
  ├─ Relative velocities
  └─ Multi-vehicle tracking
  
IMU Data (motion):
  ├─ Acceleration (x, y, z)
  ├─ Steering angle
  ├─ Angular velocity
  └─ Jerk (rate of change)
  
GPS Data:
  ├─ Current location
  ├─ Heading
  ├─ Accuracy estimate
  └─ Timestamp
  
ALPR Confidence:
  ├─ Plate string
  ├─ Confidence score (0.98 = 98%)
  ├─ Vehicle model (if detected)
  └─ Color (if detected)
  
Package size: ~50-100MB (video + telematics)
  
All encrypted with:
  ├─ AES-256 (at rest)
  ├─ TLS 1.3 (in transit)
  └─ Digital signature (tamper-proof)
```

### 5. Police Dashboard Real-Time View

```
Real-Time Information for Police:

Map View:
  ├─ CivicMesh vehicle location (GPS)
  ├─ Vehicle heading (compass direction)
  ├─ Speed displayed on map
  ├─ Matched vehicle location overlay
  └─ Route history (trail)

Live Video:
  ├─ Dashcam feed (real-time, <1 sec latency)
  ├─ Can see what detecting vehicle sees
  ├─ Quality: 1080p HD
  └─ Recording: Automatically archived

Telematics Display:
  ├─ Current OBD data (speed, RPM, heading)
  ├─ Radar detection overlay (if applicable)
  ├─ IMU acceleration/steering data
  ├─ Timestamp synchronized
  └─ Historical graph (speed over time)

Dispatch Actions:
  ├─ Send to nearest police unit
  ├─ Create incident in police CAD system
  ├─ Query past scans (retroactive)
  ├─ Enable public live stream (Amber Alert)
  ├─ Record all telematics (evidence)
  └─ Export for prosecution
```

---

## Retroactive Search Capability

### Scenario: Plate Spotted Hours Ago

```
Police: "We found the child safe. But where did the car go?"
  ↓
Query retroactive search:

GET /api/police/v1/plate-scans
{
  "plate": "ABC1234",
  "province": "ON",
  "start_time": "2024-06-04 10:00:00",  // Last 24 hours
  "end_time": "2024-06-04 14:51:20",
  "include_video": true
}

Response: All historical ALPR scans
├─ Bus #456: 14:51:20 (current)
├─ Bus #123: 10:30:15 (4+ hours ago)
├─ Van #789: 22:15:00 (yesterday)
└─ Truck #234: 08:45:00 (early morning)

For each scan, retrieve:
├─ Video clip (5-10 sec)
├─ Full telematics snapshot
├─ Location at time of scan
├─ Speed at time of scan
└─ All sensor data (radar, IMU, OBD)

Result: Complete movement history
├─ Vehicle path over 24 hours
├─ All locations where spotted
├─ Complete video record from multiple angles
├─ Prosecution evidence package
```

---

## Performance Specifications

### Matching Latency

```
ALPR scan to police notification:
  Camera frame capture:        0ms (reference)
  ALPR detection:              100-200ms (GPU accelerated)
  Database lookup:             <1ms (local)
  Evidence compilation:        200-500ms
  Upload to cloud:             200-500ms (LTE variable)
  Police dashboard update:     <100ms
  ────────────────────────────────────
  Total: <1 second
```

### Throughput

```
Per vehicle:
  ALPR scans: 30 frames/second
  Plates detected: ~2-5 per scan (multiple vehicles visible)
  Database lookups: ~60-150 per second
  Matches: 1-10 per day (typical)

Per 250 vehicle fleet:
  Total lookups: 15,000-37,500 per second
  Expected matches: 250-2,500 per day
  All processed locally (no cloud bottleneck)
```

### Storage

```
Per vehicle per day:
  ALPR scans: 2.6M frames (30fps × 24h × 3600s)
  Database size: <50MB (wanted plates cache)
  Video buffering: 1TB SSD (24-48 hour rolling buffer)
  Log files: <1GB per day
  
Cloud storage (retroactive):
  Archives evidence packages from matches
  Typical: 50-100 packages per day (across fleet)
  Storage: ~5-10GB per day
  Retention: 3 years (legal requirement)
```

---

## Database Synchronization

### Vehicle → Cloud Sync

```
Each vehicle periodically uploads:
  ├─ ALPR match logs (what plates detected, when, where)
  ├─ Telematics summaries (compressed)
  ├─ GPS traces (vehicle movement)
  ├─ Device health (sensors, uptime)
  └─ Error logs (issues encountered)

Frequency:
  ├─ On match: Immediate (emergency)
  ├─ Health data: Every 5 minutes
  ├─ Full logs: Nightly batches
  └─ Retention: Cloud keeps 90 days

Police queries:
  ├─ Retroactive plate search: Instant (cloud indexed)
  ├─ Vehicle traces: Real-time (GPS streaming)
  ├─ Historical analysis: Batch (overnight jobs)
  └─ Reports: Generated on-demand
```

### Police Database → Vehicle Sync

```
Police updates wanted plates:
  ├─ New Amber Alert
  ├─ Stolen vehicle added
  ├─ Wanted person vehicle
  └─ Removal (alert resolved)

Distribution to vehicles:
  ├─ Real-time push (immediate)
  ├─ Polling fallback (every 5 min)
  ├─ Update size: <1KB per change
  └─ Latency: <5 seconds typical

Vehicle update process:
  ├─ Receive update message
  ├─ Verify signature (authenticity)
  ├─ Update local SQLite database
  ├─ Confirm update received
  └─ Continue ALPR scanning with new data
```

---

## Privacy & Legal

### What's Stored

✅ **ALPR scans (for retroactive search):**
- Plate + location + timestamp + confidence
- Vehicle image (ALPR detected)
- Metadata (weather, lighting, etc.)

✅ **Evidence packages (on match):**
- Video clip
- Telematics
- Location
- Timestamp
- Digital signature

✗ **NOT stored:**
- Innocent driver's personal data
- Vehicle movements (non-matches)
- Civilian identity
- Biometric data

### Chain of Custody

```
Evidence preservation:
├─ Timestamp (synchronized to atomic clock)
├─ Device ID (which vehicle detected)
├─ Digital signature (tamper-proof)
├─ Encryption (AES-256)
├─ Access logs (who viewed, when)
└─ Immutable archive (3-year retention)

Court admissibility:
├─ Meets Canadian evidence law standards
├─ Chain of custody intact
├─ Technical specifications documented
├─ Calibration records available
└─ Expert testimony ready (if needed)
```

---

## Summary: Correct Architecture

**CivicMesh Amber Alert System:**

1. ✅ **Police issue alert** → Update CPIC/database with plate
2. ✅ **Fleet syncs** → Download wanted plates to local cache
3. ✅ **Local ALPR matching** → No cloud latency (<500ms total)
4. ✅ **Match found** → Send evidence package to police immediately
5. ✅ **Police sees real-time** → Operator location + telematics on dashboard
6. ✅ **Retroactive search** → Query past scans from all vehicles (24-hour window)
7. ✅ **Evidence preservation** → Chain of custody maintained, court-ready
8. ✅ **Public alerts** (optional) → Live streaming when approved by police

**Advantages:**

- ✓ Distributed matching (no single point of failure)
- ✓ Ultra-low latency (local database lookup <1ms)
- ✓ Works offline (cached database, syncs when connected)
- ✓ Scalable (250+ vehicles, 2K+ plates, no slowdown)
- ✓ Retroactive intelligence (see where vehicle went)
- ✓ Complete evidence (telematics + video + metadata)
- ✓ Court-admissible (proper chain of custody)
- ✓ Public participation (optional live streaming)

This is the correct, efficient architecture for Amber Alert detection.

