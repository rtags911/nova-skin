# Phase 6 — Functionality testing

Run on the development theme via `shopify theme dev` (127.0.0.1:9293) and the store preview link,
2026-11-19. Widths: 375px (mobile) and 1440px (desktop). Results are from the browser, not assumed.

## Checklist and results
| Area | Test | Result |
|---|---|---|
| Navigation | Every header/footer link + key templates return 200, one `<h1>`, no Liquid/translation errors | ✅ 15 URLs |
| Navigation | Shop dropdown (desktop), drawer menu (mobile), Esc closes, focus returns | ✅ |
| Navigation | Unknown URL / product → 404 template | ✅ |
| Product browsing | All 9 product pages: price, sizes, every variant available, add enabled, 3 details tabs, review widget | ✅ 9/9 |
| Variants | Size switch updates price + `?variant=` (product page only; homepage spotlight keeps `/`) | ✅ |
| Add to cart | Product page and homepage spotlight → drawer opens with the right line | ✅ |
| Cart | Typed qty 3 → subtotal ₱126 → ₱166; typed 0 removes line; Remove → empty state; badge cleared; `/cart.js` count 0 | ✅ |
| Cart | Rapid −/remove race and +++ coalescing match `/cart.js` | ✅ (fixed in 3.7) |
| Filtering | Skin concern (Brightening → 4), sort price high→low, Clear all → 9, no full reloads | ✅ |
| Search | `serum` → 6 product cards; `zzzz` → 0 results message | ✅ (fixed this phase) |
| Mobile 375px | No sideways scroll; visible controls ≥ 44px; no unlabelled controls; no `<img>` without alt | ✅ home, collection, product, cart, search, contact, blog |
| Newsletter | Footer sign-up → customer *Subscribed* + `newsletter` tag (store preview link) | ✅ (captcha blocks localhost relay; see setup.md) |
| Reviews | Judge.me widget shows "Be the first to write a review"; no sample reviews on the live page | ✅ |
| Theme editor | All 10 homepage sections listed with names; blocks expandable; "Add section" lists every preset with preview | ✅ |

## Bugs found and fixed this phase
| # | Bug | Fix |
|---|---|---|
| 1 | Search results were Skeleton's unstyled text links | `sections/search.liquid` rebuilt on the shared product grid + cards, summary as `role="status"` |
| 2 | `/collections` was an unstyled list | `sections/collections.liquid`: square tiles with product counts |
| 3 | Contact page had no form | `sections/contact-form.liquid` (`{% form 'contact' %}`) + `templates/page.contact.json` |
| 4 | Blog had no empty state; 404 was bare | Blog grid + "No posts yet"; 404 with search + back to shopping |
| 5 | Footer didn't reach the bottom on short pages | `main { flex: 1 }` in `critical.css` |
| 6 | Header logo link ~22px tall (< 44px tap target) | `.header__logo` `min-height: 2.75rem` |
| — | Pagination markup duplicated | `snippets/pagination.liquid` shared by collection, search, blog |
| — | Skeleton `image` snippet unused | removed (Theme Check `OrphanedSnippet`) |

Bugs fixed in earlier phases (kept here for the portfolio): filter race (AbortController, 3.5),
cart write race + line shifting (queue + `item.key`, 3.7), section JS not on every page (moved to snippet, 4).

## Known limitations
- **Contact template assignment:** the admin's template picker lists the *live* theme's templates.
  After publishing Nova Skin: Online Store → Pages → Contact → Theme template → `contact`.
  Until then it's previewable with `?view=contact`.
- **Test collections:** Shopify's generated "Automated Collection", "Hydrogen" and "Home page"
  still appear on `/collections`.
- **Customer accounts:** `/account` returns 404 through the local dev proxy; on the storefront
  domain Shopify routes it to the hosted customer accounts.
- **Photos / currency:** placeholder art; prices in ₱ while free-shipping copy assumes USD.
