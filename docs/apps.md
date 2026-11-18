# Apps — what's installed and why

Rule: an app is added only when it does a job the theme or Shopify can't do natively.
Every app adds scripts, data access and a dependency, so fewer is better.

| Need | Choice | Status | Why this one |
|---|---|---|---|
| Filters, related products | **Shopify Search & Discovery** (free, by Shopify) | ✅ Installed (Phase 3.5) | Required for storefront filters on product fields (Skin concern). Also tunes recommendations. |
| Product reviews | **Judge.me** (free plan) | Phase 5 | Shopify's own Product Reviews app was retired. Judge.me's free plan has an app block for the product page and writes the standard `reviews.rating` metafields, so the theme can show stars without the app's scripts. |
| Email marketing | **Shopify Email** (built in, free tier) | Phase 5 | Newsletter sign-ups are Shopify customers tagged `newsletter` with marketing consent; Shopify Email sends to them directly. No extra app or data sync. |
| Customer chat | Shopify Inbox | Optional, not installed | Useful for a live store. Adds a chat widget script on every page; not needed for a demo. |
| Analytics | Shopify Analytics (built in) | ✅ Built in | Sessions, conversion, top products out of the box. |
| Analytics (Google) | Google & YouTube channel (GA4) | Optional, not installed | Needs a real Google account and property; add for a live store. |
| Upsell | None | Not needed | Covered by Search & Discovery "complementary products" and the cart's free-shipping bar. |

## How apps connect to the theme
- **App blocks**: the product section declares `"blocks": [{"type": "@app"}]`, so app blocks
  (e.g. Judge.me's review widget) can be added in the theme editor with no code change.
- **App embeds**: site-wide app scripts are toggled in Theme settings → App embeds.
- **Metafields**: review apps write `product.metafields.reviews.rating` / `rating_count`
  (Shopify standard definitions). The product card reads those directly to show stars.
