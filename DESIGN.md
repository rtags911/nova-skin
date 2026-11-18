# NOVA SKIN — Design system

Modern minimalist, premium and quiet. Photography does the selling, typography sets the tone, and colour stays neutral with one warm clay accent.

## Colour: "porcelain & clay"
| Token | Hex | Use | Contrast on ground |
|---|---|---|---|
| `--ground` | `#F7F4EF` | page background | — |
| `--surface` | `#EDE6DC` | cards, bands | — |
| `--line` | `#D9D0C3` | borders, dividers | decorative only |
| `--ink` | `#1F1C1A` | text, primary button fill | ~15:1 |
| `--ink-muted` | `#6B635C` | secondary text | ~5.4:1 |
| `--accent` | `#8A5A3F` | links, sale price, focus ring | ~5.3:1 |

Shipped as Shopify `color_scheme_group` schemes: **Porcelain** (ground/ink), **Sand** (surface/ink), **Ink** (ink bg / ground text). Each section has a `color_scheme` setting.

## Type
- Headings: high-contrast serif. Body/UI: neutral grotesk. Both picked via `font_picker` from the Shopify font library (exact faces confirmed in Phase 3).
- Fluid scale `--text-xs … --text-4xl` (clamp), body 16px / 1.6, headings line-height 1.1 with tight tracking, labels uppercase with +0.08em tracking.

## Spacing & layout
- 4px base: `--space-1` (4) … `--space-12` (128). Section vertical padding comes from a schema `range`.
- Container max 1440px, side gutter 16px mobile / 32px tablet / 48px desktop.
- Breakpoints: base < 750 · 750 · 990. Product grid 2 / 3 / 4 columns.

## Components
- **Buttons:** primary (ink fill, ground text), secondary (1px ink outline), link (underline, accent). 48px tall, radius 2px, press `scale(.98)`, visible `:focus-visible` ring in accent.
- **Product card:** 4:5 image, hover swaps to the second image (desktop only), title, price / compare-at, quick-add. Hairline border, no shadow.
- **Inputs:** 48px, 1px `--line` border, ink on focus.
- **Drawer:** slides in from the right (transform), 360–420px wide, full width on mobile.

## Motion
transform/opacity only, 200–400ms, ease-out `cubic-bezier(.2,.7,.2,1)`. Everything is off under `prefers-reduced-motion`.
