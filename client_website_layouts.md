# Client Website Layouts — Technical Documentation

## Overview
4 layout templates (2 active, 2 TBD) that share a unified 5-bar CSS variable color system. Any color preset or custom palette applies equally to all layouts.

## Layout 1 — "Contractor Showcase" (BuildRightPros Gallery Hero)
- **File:** `src/pages/index.astro`
- **Style:** Alternating left/right sections with white content boxes + dark borders, image opposite
- **Hero:** Rotating photo gallery (`HeroGallery.astro`) with orange glow effect on active card
- **Sections:** Services, Leads, AI Callers, CRM, Pricing (4 vertical cards), Testimonials (carousel), Blog (3 cards), Contact (dark bg + white form)
- **Nav:** Sticky with brand color bg, auto-contrast text (dark/light based on luminance)
- **Best for:** Visual trades, portfolio-heavy businesses

### Component Files
| Component | Path | Purpose |
|-----------|------|---------|
| Nav | `src/components/core/Nav.astro` | Sticky nav, brand color bg, auto-contrast |
| HeroGallery | `src/components/core/HeroGallery.astro` | 5-card rotating gallery with glow |
| TestimonialCarousel | `src/components/core/TestimonialCarousel.astro` | Auto-scrolling review cards |
| ContactForm | `src/components/core/ContactForm.astro` | Dark bg contact section |
| Footer | `src/components/core/Footer.astro` | Brand color bg, auto-contrast text |
| ServiceCards | `src/components/basic/ServiceCards.astro` | 4-column trade services |
| PricingCards | `src/components/basic/PricingCards.astro` | 4-tier pricing with featured badge |
| BlogPreview | `src/components/basic/BlogPreview.astro` | 3-card blog preview |
| IconNav | `src/components/core/IconNav.astro` | 5-icon section nav (Layout 2) |

## Layout 2 — "Clean & Fast" (Mailbakery Reference)
- **File:** `src/pages/layout-2.astro`
- **Style:** Card-based with generous whitespace, large featured images
- **Hero:** Full-width single image with dark overlay + centered text
- **Sections:** Services (4 icon cards), Team (3 member cards), Testimonials (3 quote cards), Our Work (4 project gallery), Contact (dark bg + white form)
- **Nav:** Same shared Nav component
- **Best for:** Service-focused trades, content-heavy businesses

## Layout 3 & 4 — TBD
- Placeholder folders at `client-layouts/3-tbd/` and `client-layouts/4-tbd/`

---

## 5-Bar CSS Variable System

### Primary Variables (set in `src/layouts/Base.astro`)
```css
:root {
  --bg-main: #F9F7F4;     /* Bar 1: Page background */
  --dark: #1a1a1a;         /* Bar 2: Header/Footer/Nav brand color */
  --section-head: #1a1a1a; /* Bar 3: Section heading bg (auto-derived from --dark) */
  --text: #444444;         /* Bar 4: Body text color */
  --accent: #FF6B35;       /* Bar 5: Buttons, links, CTAs, icons */
}
```

### Auto-Derived Variables
```css
:root {
  --bg-alt: #EDE9E3;       /* 5% darker than --bg-main (alternating sections) */
  --accent-hover: #e55a2b; /* 10% darker than --accent */
  --text-light: #666666;   /* 30% lighter than --text */
  --border: #E8E4DF;       /* 15% darker than --bg-main */
  --accent-rgb: 255,107,53;/* RGB triplet for rgba() usage */
  --nav-text: #ffffff;     /* Auto: white if brand dark, dark if brand light */
}
```

### Preset Palettes
| Preset | bg-main | dark | section-head | text | accent |
|--------|---------|------|-------------|------|--------|
| Contractor | #F9F7F4 | #1a1a1a | #1a1a1a | #444444 | #FF6B35 |
| Professional | #F9F7F4 | #1a1a1a | #2D3748 | #444444 | #3B82F6 |
| Earth Tone | #FAF8F5 | #2C1810 | #5C4033 | #3E3028 | #8B6914 |
| Clean Green | #F5FAF5 | #1B3A1B | #2D5A2D | #2D3B2D | #22C55E |
| Modern Dark | #F7F7F7 | #111111 | #333333 | #333333 | #E11D48 |

### Color Rules
- **Never hardcode hex** in components (except `#fff` for text on dark sections)
- Use `rgba(var(--accent-rgb), 0.X)` for opacity variants
- Green checkmark `#22c55e` stays hardcoded (semantic, not themed)
- White text on dark sections stays `#fff` (not `var(--text)`)
- Nav/Footer auto-contrast: text switches white/dark based on brand color luminance

### Tailwind Integration
`tailwind.config.mjs` maps brand colors to CSS variables:
```js
brand: {
  light: 'var(--bg-main)',
  dark: 'var(--dark)',
  accent: 'var(--accent)',
  secondary: 'var(--section-head)',
  surface: '#FFFFFF',
  muted: 'var(--border)'
}
```

### Auto-Contrast (Nav + Footer)
Both Nav and Footer use `background: var(--dark)` and `color: var(--nav-text)`.
The designer wizard computes luminance of the brand color and sets `--nav-text` to `#ffffff` (dark brand) or `#1a1a1a` (light brand).

```js
function getLuminance(hex) {
  const r = parseInt(hex.slice(1, 3), 16) / 255;
  // ... WCAG relative luminance formula
  return 0.2126 * toLinear(r) + 0.7152 * toLinear(g) + 0.0722 * toLinear(b);
}
// If luminance < 0.4 → dark → white text
```

---

## File Structure
```
buildrightpros-template/
├── src/
│   ├── layouts/
│   │   └── Base.astro          ← :root CSS variables defined here
│   ├── components/
│   │   ├── core/               ← Nav, Footer, HeroGallery, ContactForm, etc.
│   │   └── basic/              ← ServiceCards, PricingCards, BlogPreview
│   └── pages/
│       ├── index.astro         ← Layout 1 (homepage)
│       ├── layout-2.astro      ← Layout 2 (Mailbakery style)
│       ├── designer.astro      ← Visual Designer wizard
│       ├── blog.astro          ← Blog page
│       └── ...
├── client-layouts/             ← Reference copies + color system spec
│   ├── 1-buildrightpros/
│   ├── 2-mailbakery/
│   ├── 3-tbd/
│   ├── 4-tbd/
│   └── color-system.md
└── tailwind.config.mjs         ← Brand colors → CSS variables
```

## Conversion Log (Phase 2)
Total hardcoded hex values replaced: ~90 instances across 9 component files.
All `rgba(255,107,53,X)` converted to `rgba(var(--accent-rgb),X)`.
Build: clean, zero errors, all 6 pages generated.
