# CLAUDE.md - AI Assistant Guide for Dropzone

## Project Overview

**Dropzone** is a React-based single-page application for tracking roller coasters at major North American theme parks. Users can explore parks via an interactive map or list view, mark coasters as "ridden," and rate them on a 5-star scale with persistent storage.

## Technology Stack

| Technology | Purpose |
|------------|---------|
| **React** | UI framework (functional components with hooks) |
| **Lucide React** | Icon library |
| **Tailwind CSS** | Utility-first styling (inline classes) |
| **SVG** | Interactive map rendering |
| **window.storage** | Custom storage API for data persistence |

## Codebase Structure

```
/home/user/Dropzone/
├── .git/                  # Version control
├── react                  # Main application file (629 lines)
│                          # Contains: PARKS data + CoasterCatalog component
└── CLAUDE.md              # This file
```

**Note:** This is an early-stage project with a single source file. No `package.json`, build config, or test setup exists yet.

## Key File: `react`

The entire application lives in a single file with two main sections:

### 1. PARKS Data Array (lines 4-154)
Static dataset containing 10 North American theme parks with 72 total coasters.

**Park Object Structure:**
```javascript
{
  id: number,           // Unique park identifier
  name: string,         // Park name
  location: string,     // City, State abbreviation
  state: string,        // Full state/province name
  lat: number,          // Latitude for map positioning
  lon: number,          // Longitude for map positioning
  coasters: Coaster[]   // Array of coasters at this park
}
```

**Coaster Object Structure:**
```javascript
{
  name: string,         // Coaster name
  speed: number,        // Top speed in mph
  height: number,       // Height in feet
  drop: number,         // Drop height in feet
  length: number,       // Track length in feet
  inversions: number,   // Number of inversions
  type: string,         // Coaster type (e.g., "Steel Giga", "Hybrid RMC")
  opened: number,       // Year opened
  povVideo: string,     // YouTube embed URL
  description: string   // Marketing description
}
```

### 2. CoasterCatalog Component (lines 156-629)
Single exported React component containing all UI and logic.

## State Management

The component uses multiple `useState` hooks:

```javascript
const [view, setView] = useState('map');               // 'map' or 'list'
const [selectedPark, setSelectedPark] = useState(null);
const [selectedCoaster, setSelectedCoaster] = useState(null);
const [riddenCoasters, setRiddenCoasters] = useState({});  // { coasterId: boolean }
const [ratings, setRatings] = useState({});                // { coasterId: number }
const [searchTerm, setSearchTerm] = useState('');
const [loading, setLoading] = useState(true);
```

**Coaster ID Format:** `{parkId}-{coasterName}` (e.g., `"1-Millennium Force"`)

## Data Persistence

Uses `window.storage` API (custom/browser storage abstraction):

| Storage Key | Data Type | Purpose |
|-------------|-----------|---------|
| `ridden-coasters` | JSON string | Object mapping coaster IDs to boolean |
| `coaster-ratings` | JSON string | Object mapping coaster IDs to 1-5 rating |

**Pattern for storage operations:**
```javascript
// Read
const result = await window.storage.get('key');
if (result?.value) {
  setData(JSON.parse(result.value));
}

// Write
await window.storage.set('key', JSON.stringify(data));
```

## UI Architecture

### View Hierarchy
```
CoasterCatalog
├── Header (stats, view toggle, search)
├── Map View (when view === 'map' && !selectedPark)
│   └── Interactive SVG map with park markers
├── List View (when view === 'list' && !selectedPark)
│   └── Grid of park cards
├── Park Details (when selectedPark && !selectedCoaster)
│   └── Grid of coaster cards with ratings
└── Coaster Modal (when selectedCoaster)
    └── Full-screen modal with video, stats, controls
```

### Map Coordinate Transformation
```javascript
// Convert lat/lon to SVG coordinates (viewBox: 0 0 1000 700)
const x = ((park.lon + 130) / 60) * 1000;
const y = ((50 - park.lat) / 25) * 600;
```

## Styling Conventions

### Color Palette (Tailwind classes)
- **Primary:** `teal-*` (backgrounds, borders)
- **Accent:** `yellow-*` (highlights, stats, active states)
- **Text:** `white`, `teal-100`, `teal-200`
- **Backgrounds:** Gradients with `bg-gradient-to-br from-teal-900 via-teal-800 to-gray-900`

### Component Patterns
- **Cards:** `bg-teal-800/30 backdrop-blur-md border border-teal-400/20 rounded-xl`
- **Buttons:** `bg-teal-600 hover:bg-teal-500 rounded-lg font-bold`
- **Active states:** `bg-teal-500` for selected, `bg-teal-800/50` for default

### Responsive Grid
```javascript
className="grid md:grid-cols-2 lg:grid-cols-3 gap-6"
```

## Helper Functions

| Function | Location | Purpose |
|----------|----------|---------|
| `loadData()` | lines 169-185 | Load persisted data on mount |
| `getCoasterId(parkId, coasterName)` | line 187 | Generate unique coaster identifier |
| `toggleRidden(parkId, coasterName)` | lines 189-198 | Toggle ridden status with persistence |
| `setRating(parkId, coasterName, rating)` | lines 200-209 | Set star rating with persistence |
| `getFilteredParks()` | lines 211-218 | Filter parks by search term |

## Development Guidelines for AI Assistants

### When Adding New Parks
1. Add to the `PARKS` array maintaining the existing structure
2. Ensure unique `id` value
3. Include accurate `lat`/`lon` for map positioning
4. Follow existing coaster data schema

### When Adding New Features
1. Maintain the single-file structure until build tooling is added
2. Use existing Tailwind class patterns for consistency
3. Follow the functional component pattern with hooks
4. Persist user data using the `window.storage` API pattern

### When Modifying UI
1. Keep the teal/yellow color scheme
2. Use backdrop blur and semi-transparent backgrounds
3. Maintain responsive grid layouts
4. Follow existing hover/transition patterns

### Code Style
- ES6+ features (arrow functions, destructuring, template literals)
- Inline Tailwind classes (no external CSS files)
- Async/await for storage operations
- Try/catch for error handling with console logging

## Known Limitations

1. **No Build System:** Missing package.json, webpack/vite config
2. **No Testing:** No test files or testing framework
3. **No TypeScript:** All JavaScript without type annotations
4. **Static Data:** Coaster data is hardcoded, not API-driven
5. **Map Approximation:** Coordinate transformation is simplified
6. **No Accessibility:** Missing ARIA labels and semantic HTML

## Future Development Priorities

When setting up the project properly:

1. Create `package.json` with dependencies:
   - react, react-dom
   - lucide-react
   - tailwindcss (with config)
   - Build tool (vite recommended)

2. Split the file into:
   - `src/data/parks.js` - PARKS constant
   - `src/components/CoasterCatalog.jsx` - Main component
   - `src/hooks/useStorage.js` - Storage logic
   - `src/utils/mapCoordinates.js` - Coordinate helpers

3. Add proper HTML entry point and Tailwind setup

## Quick Reference

| Task | Location |
|------|----------|
| Add a park | `PARKS` array (lines 4-154) |
| Modify header/stats | lines 239-294 |
| Change map behavior | lines 297-378 |
| Edit list view | lines 380-423 |
| Park details view | lines 425-502 |
| Coaster modal | lines 504-625 |
| Storage keys | `ridden-coasters`, `coaster-ratings` |
