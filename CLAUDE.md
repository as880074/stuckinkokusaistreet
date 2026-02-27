# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**卡在國際通 (Stuck in Kokusai Street)** is a static travel planning website for a 4-day/3-night Okinawa trip. The site features an interactive packing checklist, detailed daily itineraries, and integrated maps using Leaflet.js and OpenStreetMap.

## Tech Stack

- **Pure HTML5, CSS3, JavaScript (ES6+)** - No frameworks, fully static
- **Leaflet.js 1.9.4** - Free map integration with OpenStreetMap tiles
- **S51 Hand Drawn** design style - Playful, sketch-like aesthetic with custom fonts (Patrick Hand, Kalam, Caveat, Noto Sans TC)
- **LocalStorage** - Client-side state persistence for packing list

## Development Commands

### Local Development Server

```bash
# Using Python 3
python -m http.server 8000

# Using Node.js
npx http-server -p 8000

# Then open http://localhost:8000
```

No build process required - all files are served directly.

## Architecture

### File Structure Philosophy

The project follows a **modular CSS and JS architecture** where:

1. **CSS Cascade Order** (must be maintained):
   - `reset.css` - Browser resets
   - `variables.css` - CSS custom properties (S51 design tokens)
   - `common.css` - Shared components (header, footer, buttons)
   - `sketch.css` - S51 Hand Drawn style system (cards, animations)
   - Page-specific CSS (`home.css`, `itinerary.css`, `day.css`)

2. **JavaScript Module Pattern**:
   - `config.js` - MUST load first (defines CONFIG global)
   - `common.js` - Header/footer generation, toast notifications, utility functions
   - Page-specific JS (e.g., `packing-list.js`, `map.js`)

### Key Design System Concepts

**S51 Hand Drawn Style**: The entire site uses a hand-drawn, sketch-like aesthetic with:
- Wavy borders using `clip-path` (defined in `sketch.css`)
- Handwritten fonts (Patrick Hand, Kalam, Caveat)
- Organic shapes and playful animations
- Custom sketch-style buttons (`.btn-sketch`)
- Paper-like cards (`.card-sketch`)

**Color System** (in `variables.css`):
- Day-specific colors: Day 1 (#FF6B6B), Day 2 (#4ECDC4), Day 3 (#FFD93D), Day 4 (#6BCF7F)
- Text colors use warm, inviting tones

### Critical Architecture Patterns

#### 1. Shared Header/Footer System
`common.js` dynamically injects header and footer into placeholder divs:
```html
<div id="header"></div>  <!-- Auto-populated by common.js -->
<main>...</main>
<div id="footer"></div>  <!-- Auto-populated by common.js -->
```

**Important**: All HTML pages have these divs. The header/footer content is generated in `generateHeader()` and `generateFooter()` functions.

#### 2. LocalStorage State Management
The packing list uses LocalStorage for persistence:
- Key: `CONFIG.STORAGE_KEYS.PACKING_LIST`
- Structure: `{ items: {...}, expanded: {...}, customItems: [...], lastUpdated: timestamp }`
- Load on init in `packing-list.js:loadStateFromStorage()`
- Save on every change in `saveStateToStorage()`

#### 3. Map Integration (Leaflet.js)
All location data lives in `js/map.js` in the `locations` object. Maps are initialized per-page:
- `initItineraryMap()` - Overview map with all days
- `initDay1Map()`, `initDay3Map()`, `initDay4Map()` - Day-specific routes
- `initDay2Map(option)` - Special: 3 switchable route options (A/B/C)

**Map markers use emoji icons** with colored circles matching day colors.

## Important Implementation Details

### When Editing HTML Files

1. **CSS load order matters** - Always maintain: reset → variables → common → sketch → page-specific
2. **JS load order matters** - config.js must be first, then common.js
3. All pages need `<div id="header">` and `<div id="footer">` for dynamic injection
4. Leaflet CSS must come before custom CSS to allow overrides

### When Editing JavaScript

1. **Global CONFIG object** - Defined in `config.js`, contains all constants, API keys (placeholders), storage keys, translations
2. **Utility functions are global** - `showToast()`, `scrollToElement()`, `formatCurrency()`, etc. are available on `window`
3. **Translation function** - `t(key)` looks up localized strings from `CONFIG.TRANSLATIONS`

### When Adding New Pages

Follow this template structure:
```html
<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Page Title - 卡在國際通</title>

    <!-- Fonts -->
    <link href="https://fonts.googleapis.com/css2?family=Patrick+Hand&family=Kalam:wght@300;400;700&family=Caveat:wght@400;500;600;700&family=Noto+Sans+TC:wght@300;400;500;600;700&display=swap" rel="stylesheet">

    <!-- CSS: MUST maintain this order -->
    <link rel="stylesheet" href="css/reset.css">
    <link rel="stylesheet" href="css/variables.css">
    <link rel="stylesheet" href="css/common.css">
    <link rel="stylesheet" href="css/sketch.css">
    <link rel="stylesheet" href="css/[your-page].css">
</head>
<body>
    <div id="header"></div>
    <main><!-- Your content --></main>
    <div id="footer"></div>

    <!-- JS: config first, common second -->
    <script src="js/config.js"></script>
    <script src="js/common.js"></script>
    <script src="js/[your-page].js"></script>
</body>
</html>
```

### Design System Usage

**Buttons**:
- `.btn-sketch` - Primary sketch button
- `.btn-sketch-primary` - Emphasized sketch button (colored background)
- `.btn-sketch-secondary` - Outline sketch button

**Cards**:
- `.card-sketch` - Standard wavy-bordered card with shadow

**Animations**:
- `.wiggle` - Playful shake on hover (defined in sketch.css)
- Cards have subtle float-up on hover

**Responsive Breakpoints**:
- Mobile: < 768px
- Tablet: 768px - 1024px
- Desktop: > 1024px

## Common Tasks

### Adding a New Day Page
1. Copy `day1.html` as template
2. Update day-specific colors in inline styles or add to `day.css`
3. Add locations to `js/map.js` `locations` object
4. Create `initDayXMap()` function in `map.js`
5. Update navigation links in `common.js:generateHeader()`

### Modifying the Packing List
Edit the `packingListData` object in `js/packing-list.js`. Structure:
```javascript
categoryKey: {
    title: "顯示名稱",
    icon: "🪪",
    items: [
        { id: "unique-id", text: "項目文字" }
    ]
}
```

### Adding Map Locations
Add to `js/map.js` `locations` object:
```javascript
locationKey: {
    name: '景點名稱',
    coords: [latitude, longitude],
    type: 'attraction' | 'food' | 'transport' | 'hotel',
    description: '描述文字',
    icon: '🎯',  // Emoji for marker
    day: 1       // Which day (1-4)
}
```

### Updating Itinerary Content
Day content is directly in HTML files (`day1.html` - `day4.html`). Each day follows the structure:
- Hero section with day title/theme
- Day info cards (天氣、預算、交通)
- Timeline/spots section with individual spot cards
- Budget summary table
- Map section
- Day navigation

## Reference Documents

The `_bmad/` directory contains original design specs:
- `產品設計規格書-卡在國際通.md` - Complete product spec
- `技術架構文件.md` - Technical architecture (note: some outdated, actual implementation differs)
- `沖繩4天3夜行程資料.md` - Itinerary content source

**Note**: The actual implementation uses pure HTML/CSS/JS (no framework) and Leaflet.js (not Google Maps), so treat these as reference only.

## Gotchas and Important Notes

1. **Day 2 is special** - Has 3 different route options (A/B/C) with interactive switching. The map reinitializes on option change.

2. **Leaflet map initialization timing** - Maps must initialize after DOM is ready AND after the map container is visible. Use `DOMContentLoaded` event.

3. **S51 Hand Drawn wavy borders** - Don't try to use standard CSS borders. Use the pre-defined `.card-sketch` or `.btn-sketch` classes which have `clip-path` for wavy edges.

4. **Chinese typography** - Always use Noto Sans TC for Chinese text. Line-height should be generous (1.8-2.0) for readability.

5. **No API keys needed** - OpenStreetMap and Leaflet.js are completely free. The `CONFIG` object has placeholder keys but they're unused.

6. **LocalStorage limits** - Packing list state is small, but be aware of 5-10MB browser limits if expanding features.

7. **Mobile-first responsive** - All CSS should be written mobile-first, then enhanced with `min-width` media queries.

## Page-Specific Notes

### index.html (Packing List)
- Progress bar auto-updates on checkbox changes
- Custom items can be added/deleted dynamically
- Export/print functions use browser native APIs
- Category collapse/expand state persists in LocalStorage

### itinerary.html (Overview)
- Shows map with all 4 days' locations
- Uses day-specific color coding for markers
- Timeline cards link to individual day pages

### day2.html (Flexible Day)
- Unique feature: 3 route options with map switching
- Buttons toggle between Option A (美麗海水族館), B (南部文化), C (美國村)
- Map reinitializes on option change - uses `document.getElementById('day2Map').innerHTML = ''` to clear

### style-guide.html
- Living documentation of the S51 design system
- Reference for colors, typography, components
- Copy component HTML from here when building new features

## Testing Checklist

When making changes:
- [ ] Test in Chrome, Safari, Firefox
- [ ] Test responsive design (mobile, tablet, desktop)
- [ ] Verify LocalStorage saves/loads correctly (packing list)
- [ ] Check all maps render correctly
- [ ] Verify S51 hand-drawn styles are preserved
- [ ] Test header/footer appear on all pages
- [ ] Check console for errors
- [ ] Verify print styles (for packing list)

## Deployment

This is a static site. Deploy to:
- **GitHub Pages**: Push to `gh-pages` branch
- **Netlify/Vercel**: Connect repo, auto-deploy on push
- **Any static host**: Upload entire directory

No build step, no environment variables needed.
