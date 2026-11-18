# NOVA SKIN — Phase 1: Architecture & Roadmap

## Context
You want a portfolio-grade Shopify store for a fictional premium skincare brand, built as a **custom Online Store 2.0 theme** (Liquid + JSON templates + schema), to show Shopify Developer skills. Phase 1 = decide structure, design system and theme architecture before any code. Nothing Shopify exists in `D:\Ai-Web` yet. The project goes in **`D:\Ai-Web\nova-skin\`**, its own Git repo. The studio rules still apply (one folder, PRODUCT.md / DESIGN.md / design-process, no made-up reviews) but the stack is Shopify, not Next.js.

---

## 1. How a Shopify theme works (what you're learning)
A theme is a folder of Liquid, JSON, CSS and JS. Shopify renders it on their servers. You never run your own backend.

| Folder | Role | NOVA SKIN files |
|---|---|---|
| `layout/` | Outer HTML shell (`<head>`, header and footer groups, `{{ content_for_layout }}`) | `theme.liquid`, `password.liquid` |
| `templates/` | One per page type. **JSON templates** list which sections appear, in what order and with what settings. The merchant reorders them in the editor. | `index.json`, `product.json`, `collection.json`, `cart.json`, `page.json`, `page.faq.json`, `page.about.json`, `search.json`, `404.json`, `list-collections.json`, `blog.json`, `article.json`, `customers/*.json` |
| `sections/` | Reusable page modules. Each has a `{% schema %}` with settings and blocks, and these become the editor UI. Also holds **section groups** (`header-group.json`, `footer-group.json`). | hero, featured-products, … (see §5) |
| `blocks/` | **Theme blocks**: reusable blocks that can nest and be shared across sections. This is the current convention. | `text`, `button`, `image`, `benefit`, `testimonial`, `faq-item` |
| `snippets/` | Partials included with `{% render %}`. No editor settings. | `product-card`, `price`, `image` (responsive srcset), `icon`, `cart-line`, `quantity-input` |
| `assets/` | CSS, JS, SVG, fonts served from Shopify's CDN | `base.css`, `cart-drawer.js`, `product-form.js`, `facets.js` |
| `config/` | `settings_schema.json` defines global theme settings (colours, fonts, socials). `settings_data.json` holds the saved values. | |
| `locales/` | Translation strings (`en.default.json`) and editor labels (`en.default.schema.json`). No hard-coded UI text. | |

**Schema → editor link:** every `settings` entry in `{% schema %}` (type `text`, `image_picker`, `color_scheme`, `product`, `collection`, `range`…) shows up as a control in the Theme Editor. Liquid reads it as `section.settings.x` / `block.settings.x`. `presets` make a section addable from "Add section". Phase 4 goes through this step by step.

## 2. Theme base — recommendation
**Start from Shopify's Skeleton theme** (`shopify theme init`), not a Dawn fork.
- Why: a Dawn fork mostly shows that you edited someone else's 100+ files. Skeleton is minimal and follows current conventions (theme blocks, JSON templates). Every section in the repo is then yours, which is what a hiring reviewer looks for.
- Cost: we write cart drawer, facets and gallery ourselves. That's more work, but it's the code that proves the skill. Dawn stays open as a reference for Shopify's patterns.
- Vanilla JS with Web Components (`<cart-drawer>`, `<product-form>`). No framework, no build step, no npm runtime deps.

## 3. Sitemap & navigation
```
Header: Logo · Shop ▾ · Bestsellers · About · Journal · FAQ   |  Search · Account · Cart(n)
  Shop ▾ (mega/dropdown)
   ├─ By category: Cleansers · Serums · Moisturisers · Masks · Sets
   └─ By concern:  Hydration · Brightening · Anti-ageing · Sensitive skin
Footer: Shop links · Help (FAQ, Shipping & Returns, Contact) · About · Newsletter · Socials · Policies
```
**Collections** (category = manual, concern = automated by product tag):
`all`, `cleansers`, `serums`, `moisturisers`, `masks`, `sets`, `bestsellers`, `concern-hydration`, `concern-brightening`, `concern-anti-ageing`, `concern-sensitive`.
Menus are built in **Admin → Content → Menus** (`main-menu`, `footer`). The theme reads them through `linklists`.

**Pages required**
| Page | Template | Notes |
|---|---|---|
| Home | `index.json` | §4 |
| Collection | `collection.json` | grid, filters, sort |
| All collections | `list-collections.json` | |
| Product | `product.json` | §5 C |
| Cart | `cart.json` | fallback when JS is off; the drawer is the main UI |
| Search | `search.json` | predictive search later if time allows |
| About / Brand story | `page.about.json` | |
| FAQ | `page.faq.json` | |
| Contact | `page.contact.json` | `{% form 'contact' %}` |
| Shipping & Returns | `page.json` | |
| Journal (blog) + article | `blog.json`, `article.json` | one or two sample posts, good for SEO |
| 404, password | `404.json`, `password.liquid` | |
| Customer account | `customers/*` | Plain styling. New customer accounts are Shopify-hosted, so not much to theme. |
| Policies | Shopify-generated | Admin → Settings → Policies |

## 4. Homepage layout (`index.json`, top → bottom, each section with one job)
1. **Announcement bar** (header group): free-shipping threshold, so the offer is seen first
2. **Header**: navigation, search, cart count
3. **Hero**: brand promise and one CTA ("Shop serums"). Single full-bleed photo.
4. **Featured products**: bestsellers, so you can buy without browsing
5. **Shop by concern** (featured collections): route by skin problem, which is how skincare buyers think
6. **Product benefits**: 3–4 icon points (clean formulas, derm-tested, cruelty-free, recyclable). Trust.
7. **Brand story** (image-with-text): why Nova exists
8. **Featured product spotlight**: one hero SKU with an inline add-to-cart
9. **Testimonials**: social proof (see the content rule below)
10. **Promotional banner**: a set or bundle offer
11. **FAQ** (short, 4–5 questions) linking to the full FAQ page
12. **Newsletter**: `{% form 'customer' %}` with tag `newsletter`
13. **Footer** (footer group)

## 5. Templates & reusable sections
**Reusable sections (Phase 4, all with schema and presets):** `hero`, `featured-products`, `featured-collections`, `image-with-text`, `featured-product`, `benefits`, `testimonials`, `faq`, `promo-banner`, `newsletter`, `rich-text`.
**Template-specific sections (Phase 3):** `announcement-bar`, `header`, `footer`, `main-collection`, `main-product`, `product-recommendations`, `cart-drawer`, `main-cart`, `main-search`, `main-page`, `main-blog`, `main-article`, `main-404`.

**B. Collection:** `collection.products` paginated (`{% paginate %}`, 12/page). Filters come from `collection.filters` and need the **Search & Discovery** app to set which filters exist (price, product type, concern, skin type). Sorting uses `collection.sort_options`. Filtering updates the URL and re-renders the grid through the **Section Rendering API**, so there's no full reload and it still works without JS.

**C. Product (`main-product` with blocks):** gallery (media + thumbnails, swipe on mobile), title, price/compare-at, variant picker (size, e.g. 30 ml / 50 ml), quantity, add to cart, description, and collapsible tabs for **Ingredients / Benefits / How to use**. These come from **product metafields** (`custom.ingredients`, `custom.key_benefits`, `custom.how_to_use`) so the merchant edits them per product, not in code. Reviews come from the review app's **app block**. Related products load through the **Product Recommendations API** (`/recommendations/products?section_id=…&product_id=…&intent=related`).

**D. Cart drawer:** Ajax Cart API (`/cart/add.js`, `/cart/change.js`) with the `sections` param, so Shopify returns the drawer HTML already rendered. Covers quantity +/−, remove (qty 0), subtotal, empty state, a free-shipping progress bar, and focus trap / Esc / `aria-live` for accessibility.

## 6. Design system (→ `DESIGN.md`, CSS custom properties in `base.css`, editable via `settings_schema.json`)
**Palette: "porcelain & clay"** (contrast checked against the ground)
| Token | Hex | Use | Contrast on ground |
|---|---|---|---|
| `--ground` | `#F7F4EF` | page background | — |
| `--surface` | `#EDE6DC` | cards, bands | — |
| `--line` | `#D9D0C3` | borders, dividers | decorative |
| `--ink` | `#1F1C1A` | text, primary button | ~15:1 |
| `--ink-muted` | `#6B635C` | secondary text | ~5.4:1 |
| `--accent` | `#8A5A3F` | links, sale price, focus | ~5.3:1 |
Shipped as Shopify **color schemes** (`color_scheme_group`) so each section can switch between light, sand and dark. Studio rule: if you have a picture (mood board, product shot, reference site), I'll resample the palette from it.

**Type:** a refined high-contrast serif for headings and a neutral grotesk for body/UI, both chosen through `font_picker` settings (Shopify font library, served from the CDN, no licensing problems). I'll confirm the exact faces in the library in Phase 3. Fluid scale: `--text-xs … --text-4xl` with `clamp()`, body 16px, headings tracked tight, labels uppercase and spaced.
**Spacing:** 4px base, tokens `--space-1…--space-12` (4→128). Section padding comes from a schema `range` setting.
**Buttons:** primary (ink fill), secondary (outline), link. 48px tall (≥44 tap target), radius 0–2px for a crisp premium look, press state `scale(.98)`.
**Cards:** product card = 4:5 image, second image on hover (desktop), title, price, quick-add. No shadows, hairline borders only.
**Breakpoints (mobile-first):** base < 750 · `750px` tablet · `990px` desktop · max container `1440px`. 2-col grid on mobile, 3 on tablet, 4 on desktop.
**Motion:** opacity/transform only, 200–400ms, all off under `prefers-reduced-motion`.

## 7. Content rules
- Brand, products, copy and photos are **sample content**, listed in `README.md → Sample content`.
- **Testimonials and reviews:** these won't be passed off as real. The testimonials section ships with clearly marked placeholder blocks ("Sample testimonial"), and product reviews come only from the review app with test reviews you enter yourself, labelled as such in the README. Interviewers respect this.
- Photos: open-licence (Unsplash / Pexels) with credits in `README`, or AI-generated product shots, also credited.

## 8. Development roadmap & complexity
| Phase | Output | Complexity | Rough effort |
|---|---|---|---|
| 1 Planning | this doc → `docs/architecture.md`, `DESIGN.md`, `PRODUCT.md` | Low | done on approval |
| 2 Environment | Partner/Dev account, dev store, Node LTS, Git, Shopify CLI, `shopify theme init`, `theme dev`, GitHub repo, sample products/metafields/collections imported via CSV | Low–Med | 1 session |
| 3 Storefront | layout, header/footer groups, home, collection + facets, product, cart drawer | **High** (drawer and facets are the hardest parts) | 4–6 sessions |
| 4 Custom sections | 11 reusable sections, theme blocks, presets | Med | 2–3 sessions |
| 5 Apps | Search & Discovery, Judge.me, Shopify Email, Shopify Inbox, Google & YouTube | Low | 1 session |
| 6 Testing | written test checklist run on the dev store (desktop and mobile), `shopify theme check` clean, bugs logged | Med | 1–2 sessions |
| 7 Perf / SEO / a11y | responsive `image_url` + `srcset`, lazy loading, deferred JS, JSON-LD (Product, Organization), meta/OG, alt text, Lighthouse ≥ 90 mobile | Med | 1–2 sessions |
| 8 Portfolio | README (overview, stack, features, setup, screenshots, decisions, limitations), CV project blurb | Low | 1 session |

## 9. Apps (decided in Phase 5, preview)
| Need | App | Status |
|---|---|---|
| Filters, related/complementary products | **Shopify Search & Discovery** (free, by Shopify) | Required |
| Product reviews | **Judge.me** (free plan, app block) | Required for the reviews feature |
| Email marketing | **Shopify Email** (native, free tier) | Required for the newsletter flow |
| Engagement / chat | Shopify Inbox | Optional |
| Analytics | Built-in Shopify Analytics + Google & YouTube channel (GA4) | Built-in is enough; GA4 optional |
| Upsell | Covered by Search & Discovery "complementary products" and the drawer progress bar | No extra app |

## 10. Tools you'll need (installed in Phase 2, step by step)
Node.js LTS, Git, GitHub account, Shopify CLI (`npm i -g @shopify/cli@latest`), VS Code + **Shopify Liquid** extension (Theme Check built in), Shopify Partner/Dev account (free), and a development store (free, password-protected, test payments through Bogus Gateway).

## 11. Repo structure (Phase 2)
```
D:\Ai-Web\nova-skin\
  layout/ templates/ sections/ blocks/ snippets/ assets/ config/ locales/   ← theme (pushed to Shopify)
  docs/architecture.md   docs/testing.md   docs/screenshots/
  design-process/brief.md, hierarchy.md, palette.md
  PRODUCT.md  DESIGN.md  README.md  .shopifyignore  .gitignore  .theme-check.yml
```
Branches: `main` connected to the store via the **Shopify GitHub integration** (Admin → Online Store → Themes → Add theme → Connect from GitHub). Feature branches go through PRs.

## Verification for Phase 1
You review this plan. On approval I write `docs/architecture.md`, `PRODUCT.md`, `DESIGN.md` and `design-process/*` into `D:\Ai-Web\nova-skin\`. Phase 2 does not start until you say so.

## Open decisions (my defaults in bold)
- Theme base: **Skeleton** vs Dawn fork
- Palette: **porcelain & clay above** vs resample from a picture you provide
- Currency / market: **USD** (tell me if you want another)
