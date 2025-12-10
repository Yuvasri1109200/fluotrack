# Live Webcam Analysis Guide

## Overview

The MicroPlastic Detector now includes real-time webcam analysis capabilities powered by advanced computer vision. Detect, analyze, and quantify microplastics live from your webcam feed with comprehensive metrics and visualizations.

---

## Features

### Real-Time Particle Detection
- Live streaming from connected webcam
- Automatic particle detection and tracking
- Multiple particle analysis simultaneously
- Real-time frame annotation with particle contours

### Advanced Analysis Metrics
- **Size Analysis**: Area, length, width, thickness measurements
- **Shape Classification**: Fiber, Fragment, Bead, Film detection
- **Morphological Properties**: Aspect ratio, circularity, convexity
- **Texture Analysis**: Surface roughness and intensity variations
- **Risk Assessment**: Automatic risk level classification

### Live Quantification
- Particle count with real-time updates
- Size distribution (tiny, small, medium, large)
- Shape distribution analysis
- Roughness/texture classification
- Statistical metrics (min, max, median, percentile)
- FPS monitoring

### Interactive Visualizations
- Live annotated webcam feed
- Real-time statistics cards
- Dynamic distribution charts
- Particle list with detailed metrics
- Tab-based analysis view

---

## Getting Started

### Prerequisites
- Connected webcam
- Windows/Linux/macOS system
- Flask server running

### Starting Live Analysis

1. **Open Dashboard**
   - Navigate to http://localhost:5000

2. **Locate Webcam Section**
   - Scroll to "Live Webcam Analysis" section at the top
   - Below main statistics

3. **Start Webcam**
   - Click "Start Webcam" button
   - Wait for camera to initialize (2-3 seconds)
   - Live feed will appear in the video display area

4. **Monitor Analysis**
   - Watch real-time particle detection
   - View statistics updating in real-time
   - Green contours show detected particles
   - Red circles mark particle centroids
   - Blue ellipses show fitted particle shapes

---

## Analysis Metrics Explained

### Size Measurements

**Area**: Total pixel area covered by particle
- Measured in pixels²
- Larger area = larger particle

**Length**: Major axis of fitted ellipse
- Longest dimension
- Measured in pixels

**Width**: Minor axis of fitted ellipse
- Secondary dimension
- Measured in pixels

**Thickness**: Estimated z-dimension
- Estimated from particle size
- Used for 3D calculations

### Shape Classification

**Fiber**
- High aspect ratio (>3:1)
- Linear elongated structure
- Low circularity (<0.5)

**Fragment**
- Medium aspect ratio (1.5-3:1)
- Irregular irregular shape
- Medium circularity (0.5-0.7)

**Bead**
- Low aspect ratio (<1.3:1)
- High circularity (>0.8)
- Spherical appearance

**Film**
- Low aspect ratio (<1.5:1)
- Sheet-like structure
- Medium to high circularity

### Circularity Index

Formula: `4π × Area / Perimeter²`
- 0.0 = Linear
- 0.5 = Fragment
- 1.0 = Perfect circle

### Aspect Ratio

Formula: `Length / Width`
- 1.0 = Square
- >2.0 = Fibrous
- <1.2 = Spherical

### Convexity

Formula: `Particle Area / Convex Hull Area`
- Measures shape regularity
- 1.0 = Perfect convex shape
- <0.8 = Irregular structure

---

## Real-Time Analysis Tabs

### Quantification Tab

Shows detailed statistical analysis:

| Metric | Description | Unit |
|--------|-------------|------|
| Total Count | Number of detected particles | Count |
| Avg Length | Mean major axis | Pixels |
| Avg Width | Mean minor axis | Pixels |
| Avg Aspect Ratio | Average length/width | Ratio |
| Avg Circularity | Mean circularity value | 0-1 |
| Min Size | Smallest particle | px² |
| Max Size | Largest particle | px² |
| Median Size | Middle value of sizes | px² |

### Particles Tab

Lists detected particles with properties:
- Particle ID
- Size (area in px²)
- Shape type
- Aspect ratio
- Top 10 particles shown

### Distribution Tab

Visual charts showing:
- **Size Distribution**: Count of particles in each size category
- **Shape Distribution**: Fiber vs Fragment vs Bead vs Film
- **Roughness Distribution**: Smooth, Rough, Weathered classification

---

## How Detection Works

### Image Processing Pipeline

1. **Grayscale Conversion**
   - Convert RGB to grayscale
   - Prepare for analysis

2. **Bilateral Filtering**
   - Reduce noise while preserving edges
   - Kernel: 9x9

3. **Contrast Enhancement**
   - CLAHE (Contrast Limited Adaptive Histogram Equalization)
   - Tile grid: 8x8
   - Clip limit: 2.0

4. **Adaptive Thresholding**
   - Convert to binary image
   - Gaussian method
   - Block size: 11x11

5. **Morphological Operations**
   - Closing: Fill small holes
   - Opening: Remove small noise
   - Kernel: 3x3 ellipse

6. **Contour Detection**
   - Find particle boundaries
   - Extract contour properties

7. **Feature Analysis**
   - Calculate size metrics
   - Fit ellipses
   - Compute shape descriptors

### Detection Parameters

```python
min_particle_size = 50  # Pixels
max_particle_size = 10000  # Pixels
blur_kernel = (5, 5)
canny_threshold1 = 50
canny_threshold2 = 150
```

Adjust in `particle_detector.py` if needed.

---

## Saving Detected Particles

### Automatic Classification

When saving particles, the system:
1. Classifies structure based on shape
2. Determines risk level from size and shape
3. Estimates concentration
4. Calculates confidence score

### Save to Database

1. Click **"Save Particles"** button (only visible when webcam is running)
2. Detected particles are added to database
3. Each particle tagged with:
   - Unique sample ID (LIVE-FRAME-INDEX)
   - Live webcam location
   - Auto-classified structure type
   - Risk level assessment
   - Detection confidence score

### Risk Classification

| Criteria | Risk Level |
|----------|------------|
| Size > 5000 px² OR Circularity < 0.3 | Critical |
| Size > 2000 px² OR Circularity < 0.5 | High |
| Size > 500 px² | Medium |
| Size < 500 px² | Low |

---

## API Endpoints

### Webcam Control

**Start Webcam**
```
POST /api/webcam/start
```
Response: `{"message": "Webcam started", "status": "running"}`

**Stop Webcam**
```
POST /api/webcam/stop
```
Response: `{"message": "Webcam stopped", "status": "stopped"}`

**Get Status**
```
GET /api/webcam/status
```
Response:
```json
{
  "is_running": true,
  "frame_count": 156,
  "fps": 29.8,
  "particle_count": 12
}
```

**Get Frame**
```
GET /api/webcam/frame
```
Returns: JPEG image with annotations

**Get Frame (Base64)**
```
GET /api/webcam/frame/base64
```
Response:
```json
{
  "frame": "base64_encoded_jpeg",
  "timestamp": "2025-12-10T15:30:00"
}
```

### Particle Analysis

**Get Live Particles**
```
GET /api/particles/live
```
Response:
```json
{
  "particles": [...],
  "count": 12,
  "quantification": {...},
  "timestamp": "2025-12-10T15:30:00"
}
```

**Get Quantification**
```
GET /api/particles/quantification
```

**Save Particles**
```
POST /api/particles/save
Content-Type: application/json

{
  "location": "Live Webcam Feed",
  "sample_type": "live_analysis",
  "polymer_type": "Unknown"
}
```

**Get Statistics**
```
GET /api/particles/statistics
```

**Get History**
```
GET /api/particles/history
```

---

## Advanced Usage

### Python Integration

```python
import requests
import time

# Start webcam
requests.post('http://localhost:5000/api/webcam/start')

# Let it run for 10 seconds
time.sleep(10)

# Get current particles
response = requests.get('http://localhost:5000/api/particles/live')
data = response.json()

print(f"Detected {data['count']} particles")
print(f"Average size: {data['quantification']['average_size']}")

# Save particles
requests.post('http://localhost:5000/api/particles/save', json={
    'location': 'Python Script',
    'sample_type': 'live_analysis'
})

# Stop webcam
requests.post('http://localhost:5000/api/webcam/stop')
```

### Real-Time Monitoring

```python
import requests
import json
from datetime import datetime

def monitor_particles(duration=60):
    """Monitor particles for specified duration"""
    
    requests.post('http://localhost:5000/api/webcam/start')
    start = datetime.now()
    
    while (datetime.now() - start).total_seconds() < duration:
        response = requests.get('http://localhost:5000/api/particles/live')
        data = response.json()
        
        print(f"[{datetime.now().strftime('%H:%M:%S')}] Particles: {data['count']}")
        
        time.sleep(1)
    
    requests.post('http://localhost:5000/api/webcam/stop')

monitor_particles(60)
```

### Batch Processing

```python
import requests
import json

# Collect particles over time
requests.post('http://localhost:5000/api/webcam/start')

all_particles = []
for i in range(30):  # 30 seconds at ~1fps
    response = requests.get('http://localhost:5000/api/particles/live')
    particles = response.json()['particles']
    all_particles.extend(particles)
    time.sleep(1)

# Save all to file
with open('particles_batch.json', 'w') as f:
    json.dump(all_particles, f, indent=2)

requests.post('http://localhost:5000/api/webcam/stop')
```

---

## Troubleshooting

### Webcam Not Starting

**Problem**: "No webcam initialized" error

**Solutions**:
1. Check if webcam is connected
2. Verify no other application using the camera
3. Check Windows camera permissions
4. Try `ls /dev/video*` on Linux to list cameras

### Low Detection Accuracy

**Problem**: Not detecting particles or false positives

**Solutions**:
1. Improve lighting conditions
2. Clean camera lens
3. Adjust image processing parameters in `particle_detector.py`
4. Use higher contrast samples

### Performance Issues

**Problem**: Low FPS or lag

**Solutions**:
1. Reduce webcam resolution (lower resolution = faster)
2. Disable other applications
3. Use faster detection parameters
4. Increase `min_particle_size` threshold

### Memory Usage

**Problem**: High memory consumption

**Solutions**:
1. Clear particle history: Restart webcam
2. Restart Flask server
3. Limit particle history size (default: 100)

---

## Optimization Tips

### Detection Speed

1. **Increase minimum particle size** for faster detection
2. **Reduce frame resolution** in camera settings
3. **Disable complex calculations** if not needed

### Detection Accuracy

1. **Improve lighting** - Use uniform, bright lighting
2. **Increase contrast** - Use contrasting background
3. **Fine-tune thresholds** in `particle_detector.py`

### Resource Usage

1. **Update less frequently** - Increase update interval
2. **Reduce resolution** - Lower webcam resolution
3. **Limit frame rate** - Cap FPS in detection code

---

## Particle Detection Parameters

Modify in `particle_detector.py` to tune detection:

```python
class ParticleDetector:
    def __init__(self, camera_id=0):
        # Size filtering
        self.min_particle_size = 50  # Minimum pixel area
        self.max_particle_size = 10000  # Maximum pixel area
        
        # Image processing
        self.blur_kernel = (5, 5)
        self.canny_threshold1 = 50
        self.canny_threshold2 = 150
```

### Parameter Tuning Guide

| Parameter | Effect | Adjust |
|-----------|--------|--------|
| `min_particle_size` | Minimum detection size | Increase to reduce noise |
| `max_particle_size` | Maximum detection size | Decrease to ignore large objects |
| `blur_kernel` | Noise reduction | Increase for more smoothing |
| `canny_threshold1` | Edge detection low | Lower to detect more edges |
| `canny_threshold2` | Edge detection high | Higher for stricter edges |

---

## Limitations & Future Enhancements

### Current Limitations
- Single camera support
- Requires continuous processing
- No machine learning classification
- Limited polymer identification

### Planned Enhancements
- Multiple camera support
- Frame recording capability
- Machine learning model integration
- Real-time polymer classification
- 3D reconstruction
- Automated quality assessment
- Cloud integration

---

## Performance Metrics

Typical performance on modern hardware:

| Metric | Value |
|--------|-------|
| Detection FPS | 15-30 FPS |
| Particle Detection Speed | 5-10ms per frame |
| Typical Particles Detected | 5-50 per frame |
| Memory per Frame | ~2-5 MB |
| CPU Usage | 10-30% |

---

## Examples

### Example 1: Monitor Size Distribution Over Time

```python
import requests
import matplotlib.pyplot as plt

sizes_over_time = []

requests.post('http://localhost:5000/api/webcam/start')

for i in range(60):
    response = requests.get('http://localhost:5000/api/particles/quantification')
    data = response.json()['quantification']
    sizes_over_time.append(data['average_size'])
    time.sleep(1)

requests.post('http://localhost:5000/api/webcam/stop')

plt.plot(sizes_over_time)
plt.xlabel('Time (seconds)')
plt.ylabel('Average Particle Size (px²)')
plt.title('Particle Size Over Time')
plt.show()
```

### Example 2: Alert on High Particle Count

```python
import requests

requests.post('http://localhost:5000/api/webcam/start')

ALERT_THRESHOLD = 50

while True:
    response = requests.get('http://localhost:5000/api/particles/live')
    count = response.json()['count']
    
    if count > ALERT_THRESHOLD:
        print(f"⚠️ ALERT: {count} particles detected!")
    
    time.sleep(1)
```

### Example 3: Auto-Save High Risk Particles

```python
import requests

requests.post('http://localhost:5000/api/webcam/start')

while True:
    response = requests.get('http://localhost:5000/api/particles/live')
    particles = response.json()['particles']
    
    # Filter high risk
    high_risk = [p for p in particles if p['area'] > 5000]
    
    if high_risk:
        print(f"Saving {len(high_risk)} high-risk particles...")
        requests.post('http://localhost:5000/api/particles/save', json={
            'location': 'Auto-Detection',
            'sample_type': 'high_risk'
        })
    
    time.sleep(5)
```

---

## Support & Documentation

For more information:
- API Reference: See `API_REFERENCE.md`
- Usage Examples: See `EXAMPLES.md`
- Quick Start: See `QUICK_START.md`
- Main Readme: See `README.md`

---

**Last Updated**: December 2025  
**Version**: 2.0.0 (Live Webcam Analysis)
