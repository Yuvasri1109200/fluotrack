# MicroPlastic Detector Dashboard

A modern, fully functional dashboard for detecting and analyzing microplastics based on structure, shape, and size characteristics. Built with Flask backend and a responsive HTML5/CSS3/JavaScript frontend.

## Features

### 🔍 Core Functionality
- **Microplastic Detection & Recording**: Add detailed microplastic samples with comprehensive parameters
- **Advanced Filtering**: Filter by structure type, shape, polymer type, risk level, and sample type
- **Real-time Statistics**: Live dashboard statistics including total particles, average size, concentration, and risk levels
- **Interactive Charts**: Visual analysis with 5 different chart types (pie, bar, doughnut)
- **Detailed Records**: View complete microplastic information including dimensions, polymer composition, and analysis data
- **Data Export**: Export all data as JSON for external analysis

### 📊 Analysis Parameters

#### Structure Properties
- Structure Types: Fiber, Fragment, Bead, Film
- Polymer Types: PE, PET, PP, PS, PVC, LDPE, HDPE

#### Shape & Size Properties
- Shapes: Linear, Spherical, Irregular, Sheet
- Size Measurements: Length, Width, Thickness (in micrometers)
- Calculated Metrics: Area, Volume, Aspect Ratio

#### Physical Characteristics
- Color
- Transparency: Transparent, Translucent, Opaque
- Surface Texture: Smooth, Rough, Weathered
- Density

#### Risk Assessment
- Risk Levels: Low, Medium, High, Critical
- Concentration Measurement
- Confidence Score (0-100%)

#### Sample Information
- Sample Types: Water, Soil, Air, Food
- Location Tracking
- Detection Date

### 📈 Dashboard Components

1. **Statistics Cards**: 6 key metrics including total particles, average size, concentration, and risk levels
2. **Filter Panel**: Multi-parameter filtering for targeted analysis
3. **Charts Section**: 
   - Structure Distribution (Pie Chart)
   - Shape Distribution (Pie Chart)
   - Polymer Type Distribution (Pie Chart)
   - Risk Level Distribution (Horizontal Bar Chart)
   - Sample Type Distribution (Bar Chart)
4. **Data Table**: Paginated table view of all detected microplastics
5. **Detail Modal**: Comprehensive view of individual microplastic records
6. **Add/Edit Modal**: Multi-tab form for adding new microplastic records

## Installation & Setup

### Prerequisites
- Python 3.8+
- pip (Python package installer)

### Step 1: Install Dependencies
```bash
pip install -r requirements.txt
```

### Step 2: Run the Application
```bash
python server.py
```

The application will start on `http://localhost:5000`

### Step 3: Load Sample Data
1. Open the dashboard in your browser
2. Click the "Load Sample Data" button to populate the database with 50 sample microplastic records
3. Explore the dashboard with the sample data

## API Endpoints

### Microplastics Management
- `GET /api/microplastics` - Get all microplastics (with pagination and filtering)
  - Query parameters: `page`, `per_page`, `structure`, `shape`, `polymer`, `risk`, `sample_type`
- `GET /api/microplastics/<id>` - Get a specific microplastic
- `POST /api/microplastics` - Create a new microplastic record
- `PUT /api/microplastics/<id>` - Update a microplastic record
- `DELETE /api/microplastics/<id>` - Delete a microplastic record

### Statistics & Analysis
- `GET /api/statistics` - Get dashboard statistics and distributions
- `GET /api/reports` - Get all analysis reports
- `POST /api/reports` - Create a new analysis report

### Data Management
- `POST /api/import-sample-data` - Import 50 sample microplastic records
- `GET /api/export` - Export all data as JSON

## Database Schema

### Microplastic Model
```
- id: Primary Key
- sample_id: Unique sample identifier
- detection_date: DateTime of detection
- location: Sample collection location
- structure_type: Fiber, Fragment, Bead, Film
- polymer_type: PE, PET, PP, PS, PVC, LDPE, HDPE
- shape: Linear, Spherical, Irregular, Sheet
- aspect_ratio: Length/Width ratio
- length, width, thickness: Dimensions in micrometers
- area, volume: Calculated from dimensions
- color: Color description
- density: Material density
- transparency: Visual property
- surface_texture: Surface characteristics
- risk_level: Low, Medium, High, Critical
- concentration: Particles per unit
- sample_type: Water, Soil, Air, Food
- confidence_score: Detection confidence (0-100%)
```

### AnalysisReport Model
```
- id: Primary Key
- report_name: Report title
- created_date: Creation timestamp
- total_samples: Number of samples analyzed
- total_particles: Total particles detected
- average_size: Average particle size
- dominant_polymer: Most common polymer
- risk_assessment: Overall risk assessment
```

## Usage Guide

### Adding a Microplastic Record
1. Click the "Add Particle" button in the top right
2. Fill in the Basic Info tab with Sample ID and Location
3. Switch to Structure tab to select structure and polymer type
4. Go to Size & Shape tab to enter dimensions
5. Complete Analysis tab with risk level and confidence score
6. Click "Add Particle" to save

### Filtering Data
1. Use the filter dropdowns to select criteria
2. Multiple filters can be combined
3. Click "Reset" to clear all filters

### Viewing Details
1. Click the "View" button in any row to see complete information
2. View all calculated metrics (area, volume, aspect ratio)
3. Delete records from the detail modal if needed

### Exporting Data
1. Click "Export Data" to download all records as JSON
2. Use for external analysis or backup

## UI Features

### Modern Design
- Gradient backgrounds and smooth animations
- Responsive layout that works on all devices
- Smooth transitions and hover effects
- Color-coded badges for quick identification
- Toast notifications for user feedback

### Interactive Elements
- Animated floating icon in header
- Pulsing badges for critical items
- Smooth modal transitions
- Hover effects on all interactive elements
- Loading indicators for async operations

### Data Visualization
- 5 different chart types using Chart.js
- Color-coded distributions
- Interactive legends
- Real-time chart updates

## Keyboard Shortcuts (Planned)

Future versions can include:
- `Ctrl+N` - New microplastic
- `Ctrl+E` - Export data
- `Escape` - Close modals

## File Structure

```
fluotrack-advanced/
├── server.py                  # Flask application and API
├── requirements.txt           # Python dependencies
├── templates/
│   └── dashboard.html         # Main dashboard UI
├── microplastics.db           # SQLite database (auto-created)
└── README.md                  # This file
```

## Performance Notes

- Database uses SQLite for simplicity and portability
- Pagination set to 10 records per page
- Charts update on data load and filter changes
- All calculations are done server-side for accuracy

## Troubleshooting

### Port Already in Use
If port 5000 is already in use, modify `server.py`:
```python
app.run(debug=True, port=5001)  # Change 5000 to any available port
```

### Database Issues
To reset the database, delete `microplastics.db` file. It will be recreated on next run.

### No Data Appearing
1. Check browser console for JavaScript errors (F12)
2. Check server console for Python errors
3. Ensure sample data was imported using "Load Sample Data" button

## Future Enhancements

- Advanced data analysis with machine learning
- Multiple user accounts and permissions
- Real-time sensor integration
- Advanced reporting and PDF export
- Mobile app for field data collection
- Image upload and analysis
- 3D visualization of particles
- Trend analysis over time
- Environmental impact assessment

## License

This project is open source and available for research and educational purposes.

## Support

For issues, questions, or suggestions, please refer to the dashboard's built-in error messages and check the browser console for debugging information.

---

**Version**: 1.0.0  
**Last Updated**: December 2025  
**Author**: MicroPlastic Detector Development Team
