# System Architecture - Fully Live Version

## Component Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                                                                   │
│                    WEB BROWSER (Dashboard)                       │
│                                                                   │
│  ┌────────────────────┐  ┌────────────────────┐                │
│  │   Statistics       │  │     Charts         │                │
│  │  ┌──────────────┐  │  │  ┌──────────────┐  │                │
│  │  │ Total        │  │  │  │ Structure    │  │                │
│  │  │ Avg Size     │  │  │  │ Shape        │  │                │
│  │  │ Avg Conc.    │  │  │  │ Polymer      │  │                │
│  │  │ High Risk    │  │  │  │ Risk         │  │                │
│  │  │ Critical     │  │  │  │ Sample Type  │  │                │
│  │  │ Confidence   │  │  │  │              │  │                │
│  │  └──────────────┘  │  │  └──────────────┘  │                │
│  └────────────────────┘  └────────────────────┘                │
│                                                                   │
│  ┌────────────────────────────────────────────────────────────┐ │
│  │                    Live Webcam Feed                        │ │
│  │         (With particle annotations & FPS)                 │ │
│  └────────────────────────────────────────────────────────────┘ │
│                                                                   │
│  ┌────────────┐  ┌────────────┐  ┌────────────┐              │
│  │ Quantifi.  │  │ Particles  │  │Distribu.   │              │
│  │   Tab      │  │   Tab      │  │   Tab      │              │
│  └────────────┘  └────────────┘  └────────────┘              │
│                                                                   │
└─────────────────────────────────────────────────────────────────┘
                              ↑↓
              Every 100ms: updateWebcamFeed()
            ┌─────────────────────────────────┐
            │   JavaScript (dashboard.html)    │
            │                                   │
            │  ┌─────────────────────────────┐ │
            │  │ updateMainStatsFromLiveData │ │ NEW
            │  │ updateChartsFromLiveData    │ │ NEW
            │  │ updateParticlesList         │ │
            │  │ updateDistributionDisplay   │ │
            │  └─────────────────────────────┘ │
            └─────────────────────────────────┘
                              ↓
            ┌─────────────────────────────────┐
            │   Flask API (server.py)         │
            │                                   │
            │  ┌─────────────────────────────┐ │
            │  │  /api/particles/live        │ │ ← Particles + quantification
            │  │  /api/webcam/frame/base64   │ │ ← Frame with annotations
            │  │  /api/webcam/status         │ │ ← FPS stats
            │  └─────────────────────────────┘ │
            └─────────────────────────────────┘
                              ↓
            ┌─────────────────────────────────┐
            │  Particle Detector (CV Module)  │
            │  (particle_detector.py)         │
            │                                   │
            │  ┌─────────────────────────────┐ │
            │  │ Image Processing Pipeline   │ │
            │  │ ├─ Grayscale conversion     │ │
            │  │ ├─ Bilateral filtering      │ │
            │  │ ├─ CLAHE enhancement       │ │
            │  │ ├─ Adaptive thresholding    │ │
            │  │ ├─ Morphological ops       │ │
            │  │ └─ Contour detection       │ │
            │  └─────────────────────────────┘ │
            │                                   │
            │  ┌─────────────────────────────┐ │
            │  │ Feature Quantification      │ │
            │  │ ├─ Size metrics             │ │
            │  │ ├─ Shape analysis           │ │
            │  │ ├─ Texture measurements     │ │
            │  │ ├─ Risk assessment          │ │
            │  │ └─ Confidence scoring       │ │
            │  └─────────────────────────────┘ │
            │                                   │
            │  ┌─────────────────────────────┐ │
            │  │ Background Thread           │ │
            │  │ ├─ Capture loop (30 FPS)    │ │
            │  │ ├─ Frame buffering          │ │
            │  │ └─ Annotation drawing       │ │
            │  └─────────────────────────────┘ │
            └─────────────────────────────────┘
                              ↓
            ┌─────────────────────────────────┐
            │    Webcam Hardware              │
            │   (USB or Built-in Camera)      │
            └─────────────────────────────────┘
```

## Data Flow During Operation

### Timeline

```
0ms          Page Load
│            dashboard.html loads, JavaScript initializes
│
100ms        setupCharts()
│            Charts created with empty data
│
150ms        updateStatisticsCards()
│            Display "0" values
│
200ms        updateCharts()
│            Charts show empty state
│
250ms        renderTable()
│            "No microplastics found" message
│
500ms        Auto-start Webcam ✨
│            setTimeout(() => startWebcam(), 500)
│
600ms        POST /api/webcam/start
│            Server initializes ParticleDetector
│
700ms        Camera Opens
│            capture_loop() starts in background thread
│
800ms        First Frame Captured
│            Particles detection begins
│
900ms        First Particles Detected
│            detect_particles() returns array
│
1000ms       LIVE UPDATE BEGINS ↻
│            ┌─────────────────────┐
│            │ Every 100ms Loop    │
│            ├─────────────────────┤
│            │ 1. GET frame/base64 │
│            │ 2. Update IMG src   │
│            │                     │
│            │ 3. GET particles/live
│            │ 4. updateLiveAnalysis(data)
│            │    ├─ Stats cards
│            │    ├─ Charts
│            │    ├─ Particles list
│            │    └─ FPS counter
│            │                     │
│            │ 5. Next 100ms...   │
│            └─────────────────────┘
```

## Data Transformation Pipeline

```
Raw Frame (640×480 pixels)
    ↓
[Grayscale conversion]
    ↓
Gray image (640×480)
    ↓
[Bilateral Filter - noise reduction]
    ↓
Filtered gray (640×480)
    ↓
[CLAHE - contrast enhancement]
    ↓
Enhanced gray (640×480)
    ↓
[Adaptive Thresholding]
    ↓
Binary image (640×480) - foreground/background
    ↓
[Morphological Operations - closing, opening]
    ↓
Cleaned binary (640×480)
    ↓
[Contour Detection]
    ↓
Contours array [contour1, contour2, ..., contourN]
    ↓
[Filter by area: 50-10000 pixels²]
    ↓
Valid contours [valid1, valid2, ..., validM]
    ↓
[Feature extraction per contour]
    │
    ├─ Area calculation
    ├─ Centroid calculation
    ├─ Ellipse fitting
    ├─ Shape classification (fiber, fragment, bead, film)
    ├─ Circularity calculation
    ├─ Aspect ratio calculation
    ├─ Convexity calculation
    └─ Texture analysis
    ↓
Particle objects [{area, shape, AR, ...}, ...]
    ↓
[Quantification]
    │
    ├─ Count particles
    ├─ Calculate statistics (mean, std, median)
    ├─ Build distributions (size, shape, etc.)
    ├─ Assess risk levels
    └─ Calculate confidence scores
    ↓
Quantification object {count, avg_size, distributions, ...}
    ↓
[Annotation Drawing]
    │
    ├─ Draw contours (green)
    ├─ Draw centroids (red)
    ├─ Draw ellipses (blue)
    ├─ Label particles
    └─ Draw statistics text
    ↓
Annotated frame (with visual overlays)
    ↓
[JPEG Encoding]
    ↓
JPEG bytes
    ↓
[Base64 Encoding]
    ↓
JSON Response {frame: "base64...", timestamp: "..."}
    ↓
Browser receives data
    ↓
JavaScript updateLiveAnalysis(data)
    │
    ├─ Extract particles array
    ├─ Calculate main stats
    ├─ Build distributions
    ├─ Update card values
    ├─ Update chart data
    ├─ Render particles list
    └─ Update distribution bars
    ↓
UI Updates (Charts, Cards, Tables, Webcam Feed)
    ↓
User sees live results
```

## Real-Time Update Loop (Every 100ms)

```
updateWebcamFeed() {
    
    // Parallel requests (both sent immediately)
    ├─ fetch('/api/webcam/frame/base64')
    │  └─ Response: {frame: "...", timestamp: "..."}
    │     └─ Update: <img id="webcamFeed" src="data:image/jpeg;base64,..." />
    │
    └─ fetch('/api/particles/live')
       └─ Response: {
          │  particles: [{area, shape, AR, ...}, ...],
          │  count: N,
          │  quantification: {...}
          │ }
          │
          └─ updateLiveAnalysis(data) {
             │
             ├─ Update live panel
             │  ├─ liveParticleCount.textContent = N
             │  └─ liveAvgSize.textContent = avg
             │
             ├─ Update quantification tab
             │  ├─ quantCount = N
             │  ├─ quantAvgLength = avg_length
             │  ├─ quantAspectRatio = avg_AR
             │  └─ ...
             │
             ├─ updateMainStatsFromLiveData(data) ← NEW
             │  ├─ allStatistics.total_particles = count
             │  ├─ allStatistics.average_size = mean(sizes)
             │  ├─ allStatistics.high_risk_particles = count(risk=high|critical)
             │  ├─ allStatistics.critical_particles = count(risk=critical)
             │  ├─ allStatistics.average_confidence = mean(scores)
             │  └─ updateStatisticsCards() {
             │     ├─ totalParticles.textContent = N
             │     ├─ avgSize.textContent = avg
             │     ├─ highRisk.textContent = count
             │     ├─ criticalLevel.textContent = count
             │     └─ avgConfidence.textContent = %
             │  }
             │
             ├─ updateChartsFromLiveData(particles) ← NEW
             │  ├─ Build structure_distribution
             │  ├─ Build shape_distribution
             │  ├─ Build polymer_distribution
             │  ├─ Build risk_distribution
             │  ├─ Build sample_type_distribution
             │  └─ updateCharts() {
             │     ├─ charts.structure.update()
             │     ├─ charts.shape.update()
             │     ├─ charts.polymer.update()
             │     ├─ charts.risk.update()
             │     └─ charts.sampleType.update()
             │  }
             │
             ├─ updateParticlesList(particles)
             │  └─ Render top 10 particles HTML
             │
             ├─ updateDistributionDisplay(quantification)
             │  ├─ Render size distribution bars
             │  └─ Render shape distribution bars
             │
             └─ fetch('/api/webcam/status')
                └─ Response: {fps: 28.5, frame_count: 234, ...}
                   └─ liveFPS.textContent = 28.5
}

// Then in 100ms... repeat loop
```

## State Machine

```
┌─────────────────┐
│   PAGE LOADED   │
│  (Empty State)  │
└────────┬────────┘
         │
         │ setTimeout(500ms)
         ↓
┌──────────────────────┐
│ STARTING WEBCAM      │
│ POST /webcam/start   │
└────────┬─────────────┘
         │
         │ Server initializes ParticleDetector
         ↓
┌──────────────────────┐
│ WEBCAM INITIALIZED   │
│ Camera opened        │
└────────┬─────────────┘
         │
         │ capture_loop() starts
         ↓
┌──────────────────────┐
│ CAPTURING FRAMES     │
│ Getting particles    │
└────────┬─────────────┘
         │
         │ First particle detected
         ↓
┌──────────────────────┐
│ ✨ LIVE DETECTION ✨ │
│ Continuous polling   │
│ (every 100ms)        │
│                      │
│ ├─ Update webcam     │
│ ├─ Update stats      │
│ ├─ Update charts     │
│ ├─ Update particles  │
│ └─ Update FPS        │
└────────┬─────────────┘
         │
         │ User clicks "Stop Webcam"
         ↓
┌──────────────────────┐
│ STOPPING WEBCAM      │
│ POST /webcam/stop    │
└────────┬─────────────┘
         │
         │ capture_loop() exits
         │ Camera released
         ↓
┌──────────────────────┐
│ STOPPED              │
│ (User can restart)   │
└──────────────────────┘
```

## Statistics Calculation Example

```
Live Particles Detected:
[
    {area: 1200, shape: 'fiber', risk: 'high', confidence: 0.92},
    {area: 850, shape: 'fragment', risk: 'medium', confidence: 0.85},
    {area: 450, shape: 'bead', risk: 'low', confidence: 0.78},
    {area: 2100, shape: 'film', risk: 'critical', confidence: 0.88},
    {area: 600, shape: 'fragment', risk: 'medium', confidence: 0.81}
]

Statistics Calculated:
├─ count = 5
├─ average_size = (1200+850+450+2100+600) / 5 = 1040
├─ total_area = 5200
├─ high_risk_particles = 1 (risk='high')
├─ critical_particles = 1 (risk='critical')
├─ average_confidence = (0.92+0.85+0.78+0.88+0.81) / 5 = 0.848
│
├─ structure_distribution:
│  └─ {fiber: 1, fragment: 2, bead: 1, film: 1}
│
├─ shape_distribution:
│  └─ {fiber: 1, fragment: 2, bead: 1, film: 1}
│
├─ risk_distribution:
│  └─ {low: 1, medium: 2, high: 1, critical: 1}
│
└─ sample_type_distribution:
   └─ {unknown: 5}

UI Updates:
├─ Total Particles card = 5
├─ Avg Size card = 1040.00
├─ High Risk card = 1
├─ Critical Level card = 1
├─ Avg Confidence card = 84.8%
│
├─ Structure chart:
│  └─ Segments: fiber(1), fragment(2), bead(1), film(1)
│
├─ Risk chart:
│  └─ Bars: low(1), medium(2), high(1), critical(1)
│
└─ Particles list:
   ├─ #1: Size 1200 px², Shape fiber, AR 3.45
   ├─ #2: Size 850 px², Shape fragment, AR 1.89
   ├─ #3: Size 450 px², Shape bead, AR 0.98
   ├─ #4: Size 2100 px², Shape film, AR 4.21
   └─ #5: Size 600 px², Shape fragment, AR 1.67
```

---

**System is fully operational and 100% live-driven** ✅
