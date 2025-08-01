# Census Reporter AI Agent Instructions

Census Reporter is a Django web application that makes U.S. Census data more accessible through data visualizations and clean interfaces. Here's what you need to know to work effectively with this codebase:

## Key Architecture Components

1. **Backend (Django)**
- Core profile logic in `censusreporter/apps/census/profile.py`
- Uses Census API via `api.censusreporter.org` for data queries
- Geography metadata and search endpoints critical for data retrieval
- Primary data views handle ACS (American Community Survey) releases: 1-year, 3-year, 5-year

2. **Frontend**
- Profile page generation: Django templates + dynamic chart loading via `charts.js`
- Data visualization stack:
  - Maps: Mapbox GL JS for geography visualization
  - Charts: D3.js for data visualization (`static/js/charts.js`)
  - Dynamic loading through `embed.chart.frame.js`

3. **Data Flow**
- Geography -> API -> Profile Data -> Chart Rendering
- Uses consistent data structure: `geography/metadata -> tables -> values`
- Charts support multiple types: pie, column, grouped_column, histogram, bar, grouped_bar

## Core Development Workflows

1. **Local Development Setup**
```bash
git clone git@github.com:censusreporter/censusreporter.git
mkvirtualenv census --no-site-packages
pip install -r requirements.txt
python manage.py runserver
```

2. **Data Structure Pattern**
- API endpoints provide hierarchical data for profiles
- Geography parents determine comparative data display
- Chart data requires specific naming pattern: `chartType-section-subsection-detail`

## Project-Specific Conventions

1. **Chart Configuration**
- Required attributes in chart containers:
  - `id`: Determines chart type and data source
  - `data-stat-type`: Defines value formatting (percentage/number)
  - `data-chart-title`: Display title

2. **API Integration**
- Geography endpoints use Tiger2013 data by default
- Congressional districts use Tiger2013 specifically for redistricting accuracy
- API responses include coverage calculations for parent geographies

3. **Geography Handling**
- GeoJSON transformations via `reproject` module
- Support for Shapefile imports with coordinate system detection
- Geography aggregation for custom regions

## Integration Points

1. **Embedding**
- Charts can be embedded via `embed.censusreporter.org`
- Responsive iframe integration with dynamic sizing
- Map layers integrate with Mapbox GL for geography visualization

2. **External Dependencies**
- Census Bureau API for raw data
- Mapbox for map tiles
- D3.js for chart visualization

## Common Patterns

1. **Chart Creation**
```javascript
// Example chart initialization
Chart({
    chartContainer: 'container-id',
    chartType: 'histogram',
    chartData: profileData.section.data,
    chartHeight: 160,
    chartQualifier: 'Optional note'
})
```

2. **Geography Data Handling**
```python
# Common geography lookup pattern
geo_metadata = get_geography_metadata(geoid)
geo_parents = get_geography_parents(geoid)
profile_data = build_profile_data(geo_metadata, tables)
```

## Error Handling

1. **Data Release Fallbacks**
- System automatically falls back to 3-year or 5-year data when 1-year unavailable
- Uses population thresholds to determine appropriate dataset

2. **Geography Validation**
- Checks for valid FIPS codes and summary levels
- Validates geography hierarchies for display context
