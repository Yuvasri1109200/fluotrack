# API Reference - MicroPlastic Detector

Complete API documentation for the MicroPlastic Detector Dashboard backend.

## Base URL
```
http://localhost:5000
```

## Authentication
Currently no authentication required. Future versions will implement API keys.

---

## Microplastics Endpoints

### GET /api/microplastics
Retrieve all microplastics with pagination and filtering.

**Query Parameters:**
- `page` (integer): Page number (default: 1)
- `per_page` (integer): Records per page (default: 20)
- `structure` (string): Filter by structure type (fiber, fragment, bead, film)
- `shape` (string): Filter by shape (linear, spherical, irregular, sheet)
- `polymer` (string): Filter by polymer type (PE, PET, PP, PS, PVC, LDPE, HDPE)
- `risk` (string): Filter by risk level (low, medium, high, critical)
- `sample_type` (string): Filter by sample type (water, soil, air, food)

**Example Request:**
```bash
curl "http://localhost:5000/api/microplastics?page=1&per_page=10&risk=high"
```

**Response:**
```json
{
  "items": [
    {
      "id": 1,
      "sample_id": "SAMPLE-0001",
      "detection_date": "2025-12-10T15:30:00",
      "location": "River Sample A",
      "structure_type": "fiber",
      "polymer_type": "PE",
      "shape": "linear",
      "aspect_ratio": 50.5,
      "length": 1500.5,
      "width": 25.3,
      "thickness": 5.2,
      "area": 37963.65,
      "volume": 197451.78,
      "color": "transparent",
      "density": 0.92,
      "transparency": "transparent",
      "surface_texture": "smooth",
      "risk_level": "high",
      "concentration": 45.25,
      "sample_type": "water",
      "confidence_score": 95.5
    }
  ],
  "total": 50,
  "pages": 5,
  "current_page": 1
}
```

**Status Codes:**
- `200`: Success
- `400`: Invalid parameters

---

### GET /api/microplastics/{id}
Retrieve a specific microplastic by ID.

**Example Request:**
```bash
curl http://localhost:5000/api/microplastics/1
```

**Response:**
```json
{
  "id": 1,
  "sample_id": "SAMPLE-0001",
  "detection_date": "2025-12-10T15:30:00",
  ...
}
```

**Status Codes:**
- `200`: Success
- `404`: Microplastic not found

---

### POST /api/microplastics
Create a new microplastic record.

**Request Body:**
```json
{
  "sample_id": "NEW-SAMPLE-001",
  "location": "Test Location",
  "structure_type": "fiber",
  "polymer_type": "PET",
  "shape": "linear",
  "length": 500.0,
  "width": 25.0,
  "thickness": 5.0,
  "color": "blue",
  "density": 1.04,
  "transparency": "translucent",
  "surface_texture": "rough",
  "risk_level": "medium",
  "concentration": 15.5,
  "sample_type": "water",
  "confidence_score": 88.5
}
```

**Required Fields:**
- sample_id
- location
- structure_type
- polymer_type
- shape
- sample_type
- risk_level

**Optional Fields:**
- All dimensional and physical properties (will be auto-calculated if possible)

**Example Request:**
```bash
curl -X POST http://localhost:5000/api/microplastics \
  -H "Content-Type: application/json" \
  -d '{
    "sample_id": "NEW-001",
    "location": "River A",
    "structure_type": "fiber",
    "polymer_type": "PE",
    "shape": "linear",
    "length": 1000,
    "width": 50,
    "thickness": 10,
    "risk_level": "high",
    "concentration": 25.5,
    "sample_type": "water",
    "confidence_score": 92.0
  }'
```

**Response:**
```json
{
  "id": 51,
  "sample_id": "NEW-001",
  ...
}
```

**Status Codes:**
- `201`: Created successfully
- `400`: Invalid data

---

### PUT /api/microplastics/{id}
Update an existing microplastic record.

**Request Body:**
Any fields to be updated (same as POST)

**Example Request:**
```bash
curl -X PUT http://localhost:5000/api/microplastics/1 \
  -H "Content-Type: application/json" \
  -d '{
    "risk_level": "critical",
    "concentration": 75.0
  }'
```

**Response:**
Updated microplastic object

**Status Codes:**
- `200`: Success
- `400`: Invalid data
- `404`: Not found

---

### DELETE /api/microplastics/{id}
Delete a microplastic record.

**Example Request:**
```bash
curl -X DELETE http://localhost:5000/api/microplastics/1
```

**Response:**
```json
{
  "message": "Deleted successfully"
}
```

**Status Codes:**
- `204`: Deleted successfully
- `404`: Not found

---

## Statistics Endpoints

### GET /api/statistics
Retrieve dashboard statistics and distribution data.

**Example Request:**
```bash
curl http://localhost:5000/api/statistics
```

**Response:**
```json
{
  "total_particles": 50,
  "average_size": 523.45,
  "average_concentration": 32.15,
  "average_confidence": 89.75,
  "critical_particles": 8,
  "high_risk_particles": 12,
  "structure_distribution": {
    "fiber": 25,
    "fragment": 15,
    "bead": 7,
    "film": 3
  },
  "shape_distribution": {
    "linear": 25,
    "spherical": 8,
    "irregular": 12,
    "sheet": 5
  },
  "polymer_distribution": {
    "PE": 18,
    "PET": 12,
    "PP": 10,
    "PS": 7,
    "PVC": 3
  },
  "risk_distribution": {
    "low": 20,
    "medium": 15,
    "high": 12,
    "critical": 3
  },
  "sample_type_distribution": {
    "water": 25,
    "soil": 15,
    "air": 8,
    "food": 2
  }
}
```

**Status Codes:**
- `200`: Success

---

## Reports Endpoints

### GET /api/reports
Retrieve all analysis reports.

**Example Request:**
```bash
curl http://localhost:5000/api/reports
```

**Response:**
```json
[
  {
    "id": 1,
    "report_name": "Monthly Analysis - December 2025",
    "created_date": "2025-12-10T10:00:00",
    "total_samples": 50,
    "total_particles": 234,
    "average_size": 523.45,
    "dominant_polymer": "PE",
    "risk_assessment": "high"
  }
]
```

**Status Codes:**
- `200`: Success

---

### POST /api/reports
Create a new analysis report.

**Request Body:**
```json
{
  "report_name": "Weekly Report - Week 50",
  "total_samples": 50,
  "total_particles": 234,
  "average_size": 523.45,
  "dominant_polymer": "PE",
  "risk_assessment": "high"
}
```

**Example Request:**
```bash
curl -X POST http://localhost:5000/api/reports \
  -H "Content-Type: application/json" \
  -d '{
    "report_name": "November Analysis",
    "total_samples": 45,
    "total_particles": 210,
    "average_size": 498.5,
    "dominant_polymer": "PET",
    "risk_assessment": "medium"
  }'
```

**Response:**
```json
{
  "id": 2,
  "report_name": "November Analysis",
  ...
}
```

**Status Codes:**
- `201`: Created successfully
- `400`: Invalid data

---

## Data Management Endpoints

### POST /api/import-sample-data
Import 50 sample microplastic records for demonstration.

**Example Request:**
```bash
curl -X POST http://localhost:5000/api/import-sample-data
```

**Response:**
```json
{
  "message": "Successfully imported 50 sample microplastics",
  "count": 50
}
```

**Note:** This endpoint clears existing data first.

**Status Codes:**
- `201`: Data imported successfully
- `400`: Error during import

---

### GET /api/export
Export all microplastic data as JSON.

**Example Request:**
```bash
curl http://localhost:5000/api/export > microplastics_backup.json
```

**Response:**
Array of all microplastic objects

**Status Codes:**
- `200`: Success

---

## Error Responses

All errors follow this format:

```json
{
  "error": "Description of the error"
}
```

### Common Errors

**400 Bad Request**
```json
{
  "error": "Invalid polymer type"
}
```

**404 Not Found**
```json
{
  "error": "Microplastic not found"
}
```

**500 Internal Server Error**
```json
{
  "error": "Database connection failed"
}
```

---

## Data Types

### Microplastic Object
```typescript
{
  id: number,
  sample_id: string,
  detection_date: ISO8601 datetime,
  location: string,
  structure_type: "fiber" | "fragment" | "bead" | "film",
  polymer_type: "PE" | "PET" | "PP" | "PS" | "PVC" | "LDPE" | "HDPE",
  shape: "linear" | "spherical" | "irregular" | "sheet",
  aspect_ratio: number,
  length: number,
  width: number,
  thickness: number,
  area: number,
  volume: number,
  color: string,
  density: number,
  transparency: "transparent" | "translucent" | "opaque",
  surface_texture: "smooth" | "rough" | "weathered",
  risk_level: "low" | "medium" | "high" | "critical",
  concentration: number,
  sample_type: "water" | "soil" | "air" | "food",
  confidence_score: number (0-100)
}
```

---

## Rate Limiting

Currently no rate limiting. Future versions will implement per-API-key limits.

---

## Pagination

All list endpoints support pagination:

**Parameters:**
- `page`: Page number (1-indexed)
- `per_page`: Records per page (default 20, max 100)

**Response includes:**
- `items`: Array of records
- `total`: Total number of records
- `pages`: Total number of pages
- `current_page`: Current page number

---

## Filtering

Multiple filters can be combined. Filters use AND logic:

```bash
# Returns fibers with high risk level
GET /api/microplastics?structure=fiber&risk=high
```

---

## Sorting

Currently results are sorted by detection_date (newest first). Custom sorting will be added in future versions.

---

## Date Format

All dates use ISO 8601 format: `YYYY-MM-DDTHH:MM:SS`

Example: `2025-12-10T15:30:45`

---

## Version History

**v1.0.0** (Current)
- Initial release
- Full CRUD operations for microplastics
- Statistics and reporting
- Data import/export

---

## Changelog & Future Updates

### Planned for v1.1.0
- Authentication and API keys
- Rate limiting
- Custom sorting options
- Bulk operations
- Advanced filtering with ranges

### Planned for v2.0.0
- WebSocket real-time updates
- Machine learning-based risk prediction
- Image upload and analysis
- Mobile app API
- GraphQL support

---

## SDK & Libraries

### Python
```python
import requests

# Get all microplastics
response = requests.get('http://localhost:5000/api/microplastics')
data = response.json()

# Create new microplastic
new_mp = {
    "sample_id": "NEW-001",
    "location": "Test",
    "structure_type": "fiber",
    "polymer_type": "PE",
    "shape": "linear",
    "risk_level": "high",
    "concentration": 25.5,
    "sample_type": "water",
    "confidence_score": 90.0
}
response = requests.post(
    'http://localhost:5000/api/microplastics',
    json=new_mp
)
```

### JavaScript/Node.js
```javascript
// Fetch all microplastics
fetch('http://localhost:5000/api/microplastics')
  .then(res => res.json())
  .then(data => console.log(data));

// Create new microplastic
fetch('http://localhost:5000/api/microplastics', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    sample_id: 'NEW-001',
    location: 'Test',
    structure_type: 'fiber',
    polymer_type: 'PE',
    shape: 'linear',
    risk_level: 'high',
    concentration: 25.5,
    sample_type: 'water',
    confidence_score: 90.0
  })
})
.then(res => res.json())
.then(data => console.log(data));
```

---

## Support

For API questions or issues:
1. Check this documentation
2. Review the README.md
3. Check server logs
4. Test endpoints with curl or Postman

---

**API Documentation v1.0** | Last Updated: December 2025
