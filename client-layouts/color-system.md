# Client Color System — 5 Bars

## Color Bars (Visual Editor Left Menu)
Each bar is a horizontal color picker with label.

| Bar | CSS Variable     | Label            | Controls                              | Auto-derived            |
|-----|------------------|------------------|---------------------------------------|-------------------------|
| 1   | --bg-main        | Background       | Page bg, alternating section bg       | Card bg (5% lighter)    |
| 2   | --dark           | Header/Footer    | Nav bar, footer, dark sections        | —                       |
| 3   | --section-head   | Section Headings | Banner/shape behind section titles    | —                       |
| 4   | --text           | Font Color       | Body text, heading text               | Light text (auto-inverse for dark bg) |
| 5   | --accent         | Accent           | Buttons, links, icons, hovers, CTAs   | Hover state (10% darker) |

## Rules
- Simple, transferable schemes that apply to ANY layout
- No crazy/custom colors — limited palette per bar
- Logo color picker available to match client branding
- Logo creation is separate service (not included in tier)
- All 5 bars apply equally to Layout 1, 2, 3, 4

## Auto-derived Values
- --bg-alt: 5% darker than --bg-main (alternating sections)
- --accent-hover: 10% darker than --accent (hover/active states)
- --text-light: 30% lighter than --text (secondary text)
- --border: 15% darker than --bg-main (card borders, dividers)
- Card/box bg: always white or 3% lighter than --bg-main

## Preset Palettes (Quick-Select Options)
| Name             | bg-main | dark    | section-head | text    | accent  |
|------------------|---------|---------|--------------|---------|---------|
| Professional     | #F9F7F4 | #1a1a1a | #2D3748      | #444444 | #3B82F6 |
| Contractor       | #F9F7F4 | #1a1a1a | #1a1a1a      | #444444 | #FF6B35 |
| Earth Tone       | #FAF8F5 | #2C1810 | #5C4033      | #3E3028 | #8B6914 |
| Clean Green      | #F5FAF5 | #1B3A1B | #2D5A2D      | #2D3B2D | #22C55E |
| Modern Dark      | #F7F7F7 | #111111 | #333333      | #333333 | #E11D48 |
