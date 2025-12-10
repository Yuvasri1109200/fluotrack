# Quick Start Guide - MicroPlastic Detector Dashboard

## 🚀 Getting Started in 3 Steps

### Step 1: Install Dependencies (One-time setup)
```bash
pip install -r requirements.txt
```

### Step 2: Run the Server
```bash
python server.py
```

The server will start at **http://localhost:5000**

### Step 3: Open in Browser
Go to http://localhost:5000 in your web browser

---

## 📊 First Time Usage

1. **Load Sample Data**: Click the "Load Sample Data" button in the top-right corner
   - This will populate the database with 50 realistic microplastic samples
   
2. **Explore the Dashboard**:
   - View statistics cards showing key metrics
   - Check the 5 different chart visualizations
   - Browse the data table with all detected particles

3. **Try the Features**:
   - **Filter Data**: Use the dropdown filters to narrow results
   - **View Details**: Click "View" on any row to see complete information
   - **Add New**: Click "Add Particle" to manually enter microplastic data
   - **Export**: Click "Export Data" to download all data as JSON

---

## 🎯 Key Features Explained

### Statistics Cards
- **Total Particles**: Count of all detected microplastics
- **Average Size**: Mean length of particles (in micrometers)
- **Avg Concentration**: Average particle concentration
- **High Risk**: Particles marked as high or critical risk
- **Critical Level**: Dangerous particles requiring immediate attention
- **Avg Confidence**: Detection accuracy percentage

### Charts
1. **Structure Distribution**: Breakdown by fiber, fragment, bead, film
2. **Shape Distribution**: Linear, spherical, irregular, or sheet shapes
3. **Polymer Distribution**: Different polymer types detected
4. **Risk Level Distribution**: Low, medium, high, critical levels
5. **Sample Type Distribution**: Source of samples (water, soil, air, food)

### Filters
- Apply multiple filters simultaneously
- Combine structure, shape, polymer, risk level, and sample type
- Click "Reset" to clear all filters

---

## 📝 Adding a Microplastic Record

1. Click **"Add Particle"** button
2. Fill in the tabs:
   - **Basic Info**: Sample ID, Location, Sample Type
   - **Structure**: Structure type, Polymer type, Color
   - **Size & Shape**: Shape, Transparency, Dimensions
   - **Analysis**: Risk level, Concentration, Confidence score
3. Click **"Add Particle"** to save

---

## 📊 Data Fields Explained

### Microplastic Structure
- **Structure Type**: Fiber, Fragment, Bead, or Film
- **Polymer Type**: PE, PET, PP, PS, PVC, LDPE, HDPE

### Dimensions (in micrometers)
- **Length**: Longest dimension
- **Width**: Secondary dimension
- **Thickness**: Smallest dimension
- **Area**: Length × Width (auto-calculated)
- **Volume**: Length × Width × Thickness (auto-calculated)
- **Aspect Ratio**: Length ÷ Width (auto-calculated)

### Physical Properties
- **Color**: Visual appearance (transparent, white, blue, etc.)
- **Density**: Material density (g/cm³)
- **Transparency**: Transparent, Translucent, or Opaque
- **Surface Texture**: Smooth, Rough, or Weathered

### Risk Assessment
- **Risk Level**: Low, Medium, High, or Critical
- **Concentration**: Particles per unit (water, soil, etc.)
- **Confidence Score**: Detection accuracy (0-100%)

### Sample Information
- **Sample Type**: Water, Soil, Air, or Food
- **Location**: Where sample was collected
- **Detection Date**: When the particle was detected

---

## 🔍 Viewing Particle Details

Click the **"View"** button in any table row to see:
- Complete dimensions and calculations
- Physical and chemical properties
- Risk assessment details
- Detection metadata
- Option to delete the record

---

## 💾 Exporting Data

Click **"Export Data"** to download all microplastics as a JSON file. Use this for:
- External data analysis
- Backup purposes
- Integration with other tools
- Research and publication

---

## ⚙️ Troubleshooting

### Dashboard not loading?
- Ensure Flask server is running (python server.py)
- Check that port 5000 is available
- Try refreshing the page (Ctrl+R)

### No data appearing?
- Click "Load Sample Data" button
- Or manually add particles via "Add Particle" button

### Port 5000 already in use?
Edit `server.py` line 396:
```python
app.run(debug=True, port=5001)  # Change 5000 to any available port
```

### Database issues?
Delete `microplastics.db` file and restart the server. A new database will be created automatically.

---

## 📈 Advanced Usage

### API Integration
The dashboard provides RESTful APIs for programmatic access:

```bash
# Get all microplastics
curl http://localhost:5000/api/microplastics

# Get statistics
curl http://localhost:5000/api/statistics

# Add new microplastic
curl -X POST http://localhost:5000/api/microplastics \
  -H "Content-Type: application/json" \
  -d '{"sample_id":"TEST-001","location":"Test Location","structure_type":"fiber",...}'

# Export data
curl http://localhost:5000/api/export > microplastics_data.json
```

### Batch Import
Use the JSON export format to bulk import data into other systems.

---

## 🔐 Notes

- This is a development setup. For production, use a proper WSGI server (Gunicorn, Waitress)
- Data is stored in SQLite (`microplastics.db`)
- All charts and statistics update in real-time
- Pagination is set to 10 records per page

---

## 📞 Support

For issues:
1. Check browser console (F12 → Console tab)
2. Check server console for error messages
3. Review README.md for detailed documentation

---

**Happy Detecting!** 🔬🌍
