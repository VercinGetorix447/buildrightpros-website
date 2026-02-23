# Visual Designer Wizard — Technical Documentation

## Overview
The Visual Designer (`/designer`) is a live preview tool where clients pick their layout, colors, and trade to see their website in real-time. It outputs a config JSON that the `landing-page-builder` agent uses to generate the final site.

**URL:** `http://localhost:4321/designer`

## Architecture

### Page Structure
- **Left panel (340px):** 4-step wizard (layout → colors → trade → business info)
- **Right panel:** Live iframe preview showing the selected layout
- **Mobile:** Stacks wizard on top, preview below. Toggle button to show/hide wizard.

### How It Works
1. Wizard panel contains all controls
2. Iframe loads the selected layout page (`/` or `/layout-2`)
3. Color changes apply CSS variables directly to the iframe DOM via `setProperty()`
4. Trade selection injects content (headline, tagline, service names) via `querySelector()`
5. Generate button collects all state into a JSON config

---

## Step 1: Layout Picker

4 thumbnail cards in a 2x2 grid:
- **Layout 1** (Gallery Hero) — active, loads `/`
- **Layout 2** (Full Image) — active, loads `/layout-2`
- **Layout 3** — greyed out, "Coming Soon"
- **Layout 4** — greyed out, "Coming Soon"

Clicking a layout sets `iframe.src` to the layout's URL. After the iframe finishes loading, colors and trade content are re-applied via the `load` event handler.

```js
iframe.addEventListener('load', () => {
  applyColorsToIframe();
  if (currentTrade) {
    setTimeout(() => applyTradeToIframe(currentTrade), 200);
  }
});
```

## Step 2: Color Picker

### Quick Presets (5 pill buttons)
| Preset | Accent Color | Look |
|--------|-------------|------|
| Contractor | #FF6B35 (orange) | Current BRP default |
| Professional | #3B82F6 (blue) | Corporate trust |
| Earth Tone | #8B6914 (gold) | Natural warmth |
| Clean Green | #22C55E (green) | Eco/reliability |
| Modern Dark | #E11D48 (red) | Sharp/tech |

### Custom Color Bars (3 primary + 1 font)
| Order | Label | Variable | Notes |
|-------|-------|----------|-------|
| 1 | Background | `--bg-main` | Page bg color |
| 2 | Brand Color | `--dark` | Nav + footer bg. Auto-derives `--section-head`. Shows contrast hint. |
| 3 | Accent | `--accent` | Buttons, links, CTAs, glow effects |
| 4 | Font Color | `--text` | Body text. Placed last per Troy's preference. |

### Auto-Derived Colors (computed in JS)
```js
--bg-alt:       darken(bg, 5%)      // alternating section backgrounds
--accent-hover: darken(accent, 10%) // hover/active button states
--text-light:   lighten(text, 30%)  // secondary/muted text
--border:       darken(bg, 15%)     // card borders, dividers
--accent-rgb:   hexToRgb(accent)    // for rgba() usage
--nav-text:     auto                // white if brand dark, dark if brand light
--section-head: same as --dark      // section headings match brand color
```

### Auto-Contrast Logic
Uses WCAG relative luminance formula to determine if brand color is dark or light:
```js
function getLuminance(hex) {
  // sRGB to linear, then weighted sum
  return 0.2126 * R + 0.7152 * G + 0.0722 * B;
}
// luminance < 0.4 → dark → nav text white
// luminance >= 0.4 → light → nav text dark
```

The contrast hint label updates live: "Nav text: light" or "Nav text: dark"

### Debouncing
Color input events are debounced at 30ms to prevent lag during drag:
```js
let applyDebounce = null;
function applyColorsToIframe() {
  clearTimeout(applyDebounce);
  applyDebounce = setTimeout(() => { /* apply */ }, 30);
}
```

## Step 3: Trade Picker

26 trade buttons in a 2-column scrollable grid. Each trade has:
```js
{
  name: 'Plumber',
  tagline: 'Reliable plumbing you can trust',
  services: ['Pipe Repair', 'Drain Cleaning', 'Water Heater Install', 'Leak Detection']
}
```

### Trade List
Plumber, Electrician, HVAC, Roofer, General Contractor, Painter, Landscaper, Pool Cleaner, Fence Builder, Concrete, Flooring, Pest Control, Garage Door, Pressure Washing, Junk Removal, Handyman, Locksmith, Septic, Window Cleaning, Solar, Insulation, Demolition, Welding, Tree Service, Appliance Repair, Cleaning

### Content Injection
When a trade is selected, the wizard modifies the iframe DOM:
- **h1:** Business name (if entered) or trade name, uppercase
- **Hero subtitle:** Custom slogan (if entered) or trade tagline
- **Service cards:** First 4 services injected into service card headings

Selectors used:
```js
// Hero headline (works for both layouts)
doc.querySelector('.hero-content h1.slogan, .hero-overlay h1')
// Hero subtitle
doc.querySelector('.hero-content p, .hero-overlay p')
// Service cards
doc.querySelectorAll('.service-card h3, .service-item h3')
```

## Step 4: Business Info

| Field | Placeholder | Max Length | Updates |
|-------|-------------|-----------|---------|
| Business Name | "Business Name" | 28 chars | Hero h1 (live) |
| Slogan / Tagline | "Slogan / Tagline" | 60 chars | Hero subtitle (live) |
| City, State | "City, State" | — | Config JSON only |
| Phone Number | "Phone Number" | — | Config JSON only |

Business name and slogan fields update the iframe live as the user types.

## Generate Button

Collects all wizard state into a JSON config:
```json
{
  "layout": "layout-1",
  "colors": {
    "preset": "contractor",
    "bg_main": "#F9F7F4",
    "dark": "#1a1a1a",
    "section_head": "#1a1a1a",
    "text": "#444444",
    "accent": "#FF6B35"
  },
  "trade": {
    "name": "Plumber",
    "tagline": "Reliable plumbing you can trust",
    "services": ["Pipe Repair", "Drain Cleaning", "Water Heater Install", "Leak Detection"]
  },
  "business": {
    "name": "CoolAir HVAC",
    "slogan": "Comfort in every season",
    "location": "Dallas, TX",
    "phone": "(555) 123-4567"
  }
}
```

This JSON is the input format for the `landing-page-builder` agent.

---

## Known Issues / Next Steps

- [ ] Trade images: connect to iDrive e2 storage (3 images per trade, pulled by industry)
- [ ] CRM section image: use computer-with-graph photo
- [ ] Section content automation: AI-generated copy per trade per section
- [ ] Layout 2 trade picker may scroll to wrong section (minor, deferred)
- [ ] Blog.astro still has hardcoded hex values (not yet converted)

## File Location
```
src/pages/designer.astro    ← All wizard logic in single file (HTML + CSS + JS)
```
No external dependencies. Pure vanilla JS. No framework needed for the wizard.
