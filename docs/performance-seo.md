# Phase 7 — Performance, SEO and accessibility

Measured in the browser (375px viewport) against `shopify theme dev` on 2026-11-19, using the
Performance API (LCP, CLS, resource timing). The local proxy adds ~1 s to time-to-first-byte, so
absolute times are pessimistic; the relative numbers and load order are what matter.

## Where the bytes come from (product page)
| Source | Size | Files | Before `load`? | Theme can control? |
|---|---|---|---|---|
| Shopify checkout preloading (`checkout-web`) | 3,361 KB | 185 | No (idle, after load) | No — speeds up checkout |
| Dynamic checkout buttons (`portable-wallets`) | 394 KB | 2 | Yes | **Yes** — section setting |
| Judge.me app embed | 347 KB | 83 | 10 files | Partly — embed is site-wide |
| Shopify web pixels / perf-kit / shop-js | ~220 KB | 21 | Mostly no | No |
| **Nova Skin theme** (critical.css + compiled section/snippet CSS+JS) | **66 KB** | **4** | Yes | — |

## Changes
| Change | Why |
|---|---|
| Fonts preloaded with `font_url \| preload_tag` (+ `preconnect`), skipped for system fonts | The LCP element is the product `<h1>`; its font now starts downloading with the first bytes. `preload_tag` also sends a `Link` early-hint header. |
| First product image `loading="eager"` + `fetchpriority="high"`, others lazy | With real photos the first image becomes the LCP element. |
| "Show dynamic checkout buttons" setting (product: on, homepage spotlight: off) | Lets the merchant trade ~390 KB of wallet JS for speed; the homepage stays light by default. |
| Responsive `srcset`/`sizes` on every image, explicit width/height, lazy below the fold | Right-sized downloads; CLS measured at **0.000**. |
| No theme JS framework; Web Components + `{% javascript %}` (19 KB total, deferred by Shopify) | Nothing render-blocking from the theme. |

**After:** LCP ≈ 80 ms after first byte (h1), CLS 0.000, theme payload 66 KB / 4 files.

## SEO
| Item | Status |
|---|---|
| `<title>` per page (+ page number, store name) | ✅ (Skeleton meta-tags) |
| Meta description | ✅ when set — products have one; set the homepage one in Online Store → Preferences and each collection's under *Search engine listing* |
| Canonical URL | ✅ collection-scoped product URLs canonicalise to `/products/…` |
| Open Graph / Twitter cards | ✅ + new *Default social sharing image* setting for pages without an image |
| Structured data | ✅ Product/ProductGroup (Shopify `structured_data`), **Organization**, **WebSite + SearchAction** (home), **BreadcrumbList** (collection, product), **FAQPage** (FAQ section blocks), **Article** — all verified to parse as valid JSON |
| Image alt text | ✅ product images fall back to product title; decorative images `alt=""` |

## Accessibility
| Check | Result |
|---|---|
| One `<h1>`, no skipped heading levels | ✅ after adding visually hidden "Products"/"Search results" `<h2>` on grid pages |
| Landmarks (header, nav, main, footer), skip link, `lang` | ✅ every page |
| Text contrast (all 3 colour schemes, 10 pairs) | ✅ all ≥ 4.5:1 (lowest 5.30:1) |
| Tap targets ≥ 44px, labelled controls | ✅ (Phase 6 audit) |
| Keyboard: menus, drawer (`<dialog>`), filters, cart, focus return | ✅ (Phases 3.2, 3.5, 3.7) |
| `prefers-reduced-motion` | ✅ global rule in critical.css; JS smooth-scroll checks the media query |

## Bug found
`{search_term_string}` inside a `{{ … }}` tag broke Liquid parsing (the `}` ended the tag) — the
preview showed *Failed to Upload Theme Files*. Theme Check didn't flag it. Fixed by keeping the
placeholder in plain text outside the output tag.

## Not measurable here
A real Lighthouse score needs the published storefront: the dev store is password-protected and the
local proxy distorts timings. Run Lighthouse / PageSpeed Insights after launch.
