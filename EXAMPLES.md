# Usage Examples & Code Samples

Practical examples for using the MicroPlastic Detector Dashboard API and UI.

---

## Table of Contents
1. [Basic Setup](#basic-setup)
2. [UI Usage Examples](#ui-usage-examples)
3. [Python API Examples](#python-api-examples)
4. [JavaScript/Node.js Examples](#javascriptnodejs-examples)
5. [Data Analysis](#data-analysis)
6. [Common Workflows](#common-workflows)

---

## Basic Setup

### Starting the Application
```bash
# Navigate to project directory
cd fluotrack-advanced

# Install dependencies (first time only)
pip install -r requirements.txt

# Run the server
python server.py

# Open in browser
# http://localhost:5000
```

---

## UI Usage Examples

### Example 1: Finding High-Risk Microplastics

1. Open the dashboard at http://localhost:5000
2. In the **Filters** section, select:
   - Risk Level: "High"
   - Click the filter will auto-apply
3. View the filtered results in the table
4. Click "View" on any row for complete details

### Example 2: Analyzing Polymer Distribution

1. Load sample data with "Load Sample Data" button
2. Look at the **Polymer Type Distribution** pie chart
3. The chart shows which polymers are most prevalent
4. Use the "Polymer Type" filter to see detailed records

### Example 3: Adding a Water Sample

1. Click **"Add Particle"** button
2. **Basic Info tab:**
   - Sample ID: `RIVER-001`
   - Location: `North River`
   - Sample Type: `water`
3. **Structure tab:**
   - Structure Type: `fiber`
   - Polymer Type: `PE`
   - Color: `transparent`
4. **Size & Shape tab:**
   - Shape: `linear`
   - Length: `2500`
   - Width: `50`
   - Thickness: `15`
   - Transparency: `transparent`
   - Surface Texture: `smooth`
5. **Analysis tab:**
   - Risk Level: `medium`
   - Concentration: `18.5`
   - Confidence Score: `92`
6. Click **"Add Particle"**

### Example 4: Exporting Data for Research

1. Click **"Export Data"** button
2. Browser downloads `microplastics_data.json`
3. Use the JSON data in:
   - Excel/Google Sheets
   - Python pandas analysis
   - R statistical analysis
   - Other tools

---

## Python API Examples

### Example 1: Fetch All Microplastics

```python
import requests
import json

# Get all microplastics
response = requests.get('http://localhost:5000/api/microplastics')
microplastics = response.json()

print(f"Total pages: {microplastics['pages']}")
print(f"Total records: {microplastics['total']}")

# Display first microplastic
if microplastics['items']:
    first = microplastics['items'][0]
    print(f"\nFirst microplastic:")
    print(f"  Sample ID: {first['sample_id']}")
    print(f"  Structure: {first['structure_type']}")
    print(f"  Risk Level: {first['risk_level']}")
```

### Example 2: Filter Microplastics with Risk Level

```python
import requests

# Get only high-risk microplastics
params = {
    'risk': 'high',
    'page': 1,
    'per_page': 20
}

response = requests.get(
    'http://localhost:5000/api/microplastics',
    params=params
)

high_risk = response.json()['items']

print(f"Found {len(high_risk)} high-risk particles")
for mp in high_risk:
    print(f"  - {mp['sample_id']}: {mp['polymer_type']}")
```

### Example 3: Create Multiple Microplastics

```python
import requests

microplastics_data = [
    {
        "sample_id": "BATCH-001",
        "location": "Ocean A",
        "structure_type": "fiber",
        "polymer_type": "PE",
        "shape": "linear",
        "length": 1500,
        "width": 25,
        "thickness": 5,
        "risk_level": "high",
        "concentration": 35.5,
        "sample_type": "water",
        "confidence_score": 95.0
    },
    {
        "sample_id": "BATCH-002",
        "location": "Ocean B",
        "structure_type": "fragment",
        "polymer_type": "PET",
        "shape": "irregular",
        "length": 800,
        "width": 600,
        "thickness": 50,
        "risk_level": "medium",
        "concentration": 22.0,
        "sample_type": "water",
        "confidence_score": 88.5
    }
]

for data in microplastics_data:
    response = requests.post(
        'http://localhost:5000/api/microplastics',
        json=data
    )
    
    if response.status_code == 201:
        result = response.json()
        print(f"✓ Created: {result['sample_id']}")
    else:
        print(f"✗ Error: {response.json()}")
```

### Example 4: Statistical Analysis with Pandas

```python
import requests
import pandas as pd

# Fetch all microplastics
all_data = []
page = 1

while True:
    response = requests.get(
        'http://localhost:5000/api/microplastics',
        params={'page': page, 'per_page': 100}
    )
    
    data = response.json()
    all_data.extend(data['items'])
    
    if page >= data['pages']:
        break
    page += 1

# Create DataFrame
df = pd.DataFrame(all_data)

# Analysis examples
print("=== Microplastic Analysis ===\n")

# Average size by polymer
print("Average size by polymer type:")
print(df.groupby('polymer_type')['length'].mean())

# Risk level distribution
print("\nRisk level distribution:")
print(df['risk_level'].value_counts())

# Concentration stats
print("\nConcentration statistics:")
print(df['concentration'].describe())

# Sample type breakdown
print("\nParticles by sample type:")
print(df['sample_type'].value_counts())
```

### Example 5: Get Dashboard Statistics

```python
import requests
import json

# Fetch statistics
response = requests.get('http://localhost:5000/api/statistics')
stats = response.json()

print("=== Dashboard Statistics ===\n")
print(f"Total Particles: {stats['total_particles']}")
print(f"Average Size: {stats['average_size']} μm")
print(f"Average Concentration: {stats['average_concentration']} particles/unit")
print(f"Critical Level Particles: {stats['critical_particles']}")

print("\n=== Structure Distribution ===")
for structure, count in stats['structure_distribution'].items():
    percentage = (count / stats['total_particles']) * 100
    print(f"{structure}: {count} ({percentage:.1f}%)")

print("\n=== Polymer Distribution ===")
for polymer, count in stats['polymer_distribution'].items():
    print(f"{polymer}: {count}")
```

### Example 6: Delete Low-Confidence Records

```python
import requests

# Get all microplastics with low confidence
response = requests.get('http://localhost:5000/api/microplastics')
all_microplastics = response.json()['items']

# Find low confidence records
low_confidence = [
    mp for mp in all_microplastics
    if mp['confidence_score'] < 70
]

print(f"Found {len(low_confidence)} low-confidence records")

# Delete them
for mp in low_confidence:
    response = requests.delete(
        f"http://localhost:5000/api/microplastics/{mp['id']}"
    )
    
    if response.status_code == 204:
        print(f"✓ Deleted: {mp['sample_id']}")
    else:
        print(f"✗ Error deleting {mp['sample_id']}")
```

---

## JavaScript/Node.js Examples

### Example 1: Browser - Filter and Display

```javascript
// Fetch high-risk microplastics and display in console
async function getHighRiskParticles() {
    const response = await fetch('/api/microplastics?risk=high&per_page=50');
    const data = await response.json();
    
    console.log(`Found ${data.items.length} high-risk particles:`);
    
    data.items.forEach(mp => {
        console.log(`
            Sample: ${mp.sample_id}
            Structure: ${mp.structure_type}
            Polymer: ${mp.polymer_type}
            Concentration: ${mp.concentration} particles/unit
        `);
    });
}

// Call the function
getHighRiskParticles();
```

### Example 2: Create and Monitor

```javascript
// Create a new microplastic and check the result
async function addNewMicroplastic() {
    const newMP = {
        sample_id: `SAMPLE-${Date.now()}`,
        location: "New Location",
        structure_type: "fiber",
        polymer_type: "PE",
        shape: "linear",
        length: 1200,
        width: 40,
        thickness: 8,
        risk_level: "medium",
        concentration: 20.5,
        sample_type: "water",
        confidence_score: 85.5
    };
    
    try {
        const response = await fetch('/api/microplastics', {
            method: 'POST',
            headers: { 'Content-Type': 'application/json' },
            body: JSON.stringify(newMP)
        });
        
        if (response.ok) {
            const created = await response.json();
            console.log(`✓ Created with ID: ${created.id}`);
        } else {
            console.error('Failed to create');
        }
    } catch (error) {
        console.error('Error:', error);
    }
}

addNewMicroplastic();
```

### Example 3: Node.js - Batch Analysis

```javascript
const fetch = require('node-fetch');

async function analyzeAllData() {
    try {
        // Fetch statistics
        const statsResponse = await fetch('http://localhost:5000/api/statistics');
        const stats = await statsResponse.json();
        
        console.log('=== MICROPLASTIC ANALYSIS ===\n');
        console.log(`Total Particles: ${stats.total_particles}`);
        console.log(`Average Size: ${stats.average_size} μm`);
        console.log(`Critical Particles: ${stats.critical_particles}\n`);
        
        // Analyze distribution
        console.log('Structure Distribution:');
        Object.entries(stats.structure_distribution).forEach(([type, count]) => {
            const pct = (count / stats.total_particles * 100).toFixed(1);
            console.log(`  ${type}: ${count} (${pct}%)`);
        });
        
        console.log('\nRisk Distribution:');
        Object.entries(stats.risk_distribution).forEach(([level, count]) => {
            console.log(`  ${level}: ${count}`);
        });
        
    } catch (error) {
        console.error('Error:', error);
    }
}

analyzeAllData();
```

---

## Data Analysis

### Example 1: Identify Problem Areas

```python
import requests
from collections import Counter

# Get all critical particles
response = requests.get('http://localhost:5000/api/microplastics?risk=critical')
critical = response.json()['items']

# Find most common locations
locations = Counter(mp['location'] for mp in critical)

print("Critical Particles by Location:")
for location, count in locations.most_common():
    print(f"  {location}: {count}")

# Find most common polymers in critical
polymers = Counter(mp['polymer_type'] for mp in critical)

print("\nCritical Polymers:")
for polymer, count in polymers.most_common():
    print(f"  {polymer}: {count}")
```

### Example 2: Size Category Analysis

```python
import requests

response = requests.get('http://localhost:5000/api/microplastics?per_page=200')
all_particles = response.json()['items']

# Categorize by size
small = [mp for mp in all_particles if mp['length'] < 100]
medium = [mp for mp in all_particles if 100 <= mp['length'] < 1000]
large = [mp for mp in all_particles if mp['length'] >= 1000]

print(f"Small particles (<100 μm): {len(small)}")
print(f"Medium particles (100-1000 μm): {len(medium)}")
print(f"Large particles (>1000 μm): {len(large)}")

# Risk by size
print("\nRisk distribution by size:")
for category, particles in [("Small", small), ("Medium", medium), ("Large", large)]:
    if particles:
        critical = sum(1 for p in particles if p['risk_level'] == 'critical')
        print(f"{category}: {critical} critical out of {len(particles)}")
```

---

## Common Workflows

### Workflow 1: Daily Sample Collection and Analysis

```python
import requests
from datetime import datetime

def log_daily_samples(samples_data):
    """Log multiple samples collected during the day"""
    
    successful = 0
    failed = 0
    
    for sample in samples_data:
        response = requests.post(
            'http://localhost:5000/api/microplastics',
            json=sample
        )
        
        if response.status_code == 201:
            successful += 1
        else:
            failed += 1
            print(f"Failed to add {sample['sample_id']}")
    
    # Get updated statistics
    stats_response = requests.get('http://localhost:5000/api/statistics')
    stats = stats_response.json()
    
    print(f"""
    Daily Report - {datetime.now().strftime('%Y-%m-%d')}
    ================================
    Added: {successful} samples
    Failed: {failed} samples
    
    Total particles in system: {stats['total_particles']}
    Critical level: {stats['critical_particles']}
    """)

# Usage
today_samples = [
    {
        "sample_id": "2025-12-10-001",
        "location": "River A",
        "structure_type": "fiber",
        "polymer_type": "PE",
        "shape": "linear",
        "length": 1500,
        "width": 30,
        "thickness": 10,
        "risk_level": "high",
        "concentration": 35.5,
        "sample_type": "water",
        "confidence_score": 92.5
    },
    # ... more samples
]

log_daily_samples(today_samples)
```

### Workflow 2: Generate Weekly Report

```python
import requests
from datetime import datetime, timedelta

def generate_weekly_report():
    """Generate a comprehensive weekly report"""
    
    # Get current statistics
    response = requests.get('http://localhost:5000/api/statistics')
    stats = response.json()
    
    # Create report
    report_data = {
        "report_name": f"Weekly Report - Week {datetime.now().isocalendar()[1]}",
        "total_samples": stats['total_particles'],  # Using as samples
        "total_particles": stats['total_particles'],
        "average_size": stats['average_size'],
        "dominant_polymer": max(
            stats['polymer_distribution'].items(),
            key=lambda x: x[1]
        )[0],
        "risk_assessment": "high" if stats['critical_particles'] > 10 else "medium"
    }
    
    # Save report
    response = requests.post(
        'http://localhost:5000/api/reports',
        json=report_data
    )
    
    if response.status_code == 201:
        report = response.json()
        print(f"✓ Report created: {report['report_name']}")
    else:
        print("✗ Failed to create report")

generate_weekly_report()
```

### Workflow 3: Backup and Archive

```python
import requests
import json
from datetime import datetime

def backup_database():
    """Backup all data as JSON with timestamp"""
    
    response = requests.get('http://localhost:5000/api/export')
    all_data = response.json()
    
    # Create filename with timestamp
    timestamp = datetime.now().strftime('%Y-%m-%d_%H-%M-%S')
    filename = f'microplastics_backup_{timestamp}.json'
    
    # Save to file
    with open(filename, 'w') as f:
        json.dump(all_data, f, indent=2)
    
    print(f"✓ Backed up {len(all_data)} records to {filename}")
    
    return filename

# Run backup
backup_file = backup_database()
```

---

## Troubleshooting Examples

### Debug API Response

```python
import requests
import json

try:
    response = requests.get('http://localhost:5000/api/microplastics')
    print(f"Status Code: {response.status_code}")
    print(f"Headers: {dict(response.headers)}")
    print(f"Body:\n{json.dumps(response.json(), indent=2)}")
except requests.exceptions.ConnectionError:
    print("✗ Could not connect. Is the server running?")
except json.JSONDecodeError:
    print("✗ Invalid JSON response")
```

### Check Database

```python
import requests

# Get basic statistics to verify connection
response = requests.get('http://localhost:5000/api/statistics')

if response.status_code == 200:
    stats = response.json()
    if stats['total_particles'] > 0:
        print("✓ Database is working and contains data")
    else:
        print("⚠ Database is empty. Load sample data from the UI.")
else:
    print("✗ API error")
```

---

## Performance Tips

1. **Use pagination**: Don't fetch all records at once
   ```python
   # Good
   fetch('/api/microplastics?page=1&per_page=100')
   
   # Bad - loads entire dataset
   fetch('/api/microplastics?per_page=10000')
   ```

2. **Filter server-side**: Filter in the query, not in code
   ```python
   # Good
   fetch('/api/microplastics?risk=high&polymer=PE')
   
   # Bad - fetches all then filters
   fetch('/api/microplastics')  # then filter in Python
   ```

3. **Cache statistics**: Don't call statistics endpoint repeatedly
   ```python
   # Cache results
   stats = requests.get('/api/statistics').json()
   # Use stats multiple times
   ```

---

## Next Steps

- Integrate with your data collection system
- Build automated reports
- Create dashboards for different users
- Implement machine learning predictions
- Build mobile app for field collection

---

**Last Updated**: December 2025
