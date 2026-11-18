# NOVA SKIN — portfolio and application copy

Use whichever length fits. Every number here comes from the project's own docs; adjust the
wording to your voice, but keep the facts as they are.

## One line (CV / LinkedIn headline project)
Custom Shopify Online Store 2.0 theme for a skincare brand: Liquid sections and theme blocks,
AJAX cart drawer, Section Rendering API filters, metafield-driven product pages, structured data.

## CV bullets
- Built a custom Shopify theme from the Skeleton starter with 27 sections, 6 theme blocks and 14 snippets, all editable in the theme editor through schema settings and presets.
- Implemented collection filtering and sorting with Shopify storefront filtering and the Section Rendering API, with shareable URLs, Back-button support and a no-JavaScript fallback.
- Built an AJAX cart drawer on a native `<dialog>` using the Cart API; queued writes and stable line keys fixed a race that dropped item removals.
- Modelled product content (ingredients, benefits, usage, skin concern) as metafields and set up store data with the Admin GraphQL API (collections, menus, metafields, inventory).
- Added Product, Organization, Breadcrumb, FAQ and Article structured data, font preloading and WCAG AA contrast; the theme's own code is 66 KB with zero layout shift.

## Application paragraph
I built NOVA SKIN, a custom Shopify theme for a fictional premium skincare brand, to show how I
work on Shopify themes end to end. It starts from Shopify's Skeleton theme and uses current
Online Store 2.0 patterns: JSON templates, section groups, theme blocks and app blocks, so a
merchant can rebuild the homepage in the theme editor without touching code. Collection filters
and sorting update in place through the Section Rendering API and still work without JavaScript;
the cart is a native `<dialog>` drawer on the AJAX Cart API. Product details live in metafields,
and I set up the store's collections, menus and inventory through the Admin GraphQL API. I tested
every flow in the browser at mobile and desktop widths, logged the bugs I found and how I fixed
them, and kept the theme's own payload to 66 KB. The code, screenshots and write-up are on GitHub:
https://github.com/rtags911/nova-skin

## Interview talking points
1. **Why Skeleton, not a Dawn fork**: every section in the repo is my own work; Dawn stayed a reference.
2. **Progressive enhancement**: filters, product form and cart are real forms first; Web Components upgrade them.
3. **Two concurrency bugs**: abort stale *reads* (filters), but queue *writes* (cart), because aborting a fetch doesn't stop Shopify applying it; address cart lines by `item.key`, not position.
4. **Section vs snippet JavaScript**: Shopify bundles section JS only where the section renders and snippet JS everywhere, which is why `<product-info>` lives in a snippet.
5. **Metafields over hard-coding**: product copy and the Skin concern filter come from typed metafields with fixed choices, so merchants edit them and filters stay clean.
6. **Honest content**: sample testimonials carry a visible label; ratings render only from real review data.
7. **Performance in context**: most of the page weight is Shopify checkout preloading after load; I measured what the theme controls and made dynamic checkout a merchant setting.

## Before sharing
- Add real or properly licensed product photos and retake the screenshots.
- Settle the store currency (USD vs PHP) so prices and free-shipping copy agree.
- Optionally publish the theme, or share the draft's preview link from Online Store → Themes.
