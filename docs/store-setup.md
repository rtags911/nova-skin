# Store setup checklist (Shopify admin)

**Status (2026-11-19):** sections 1–3 done via the Shopify GraphiQL app (Admin API 2026-10):
`collectionCreate` ×10 with tag `ruleSet` → `publishablePublish` to Online Store → `menuUpdate` on `main-menu`
→ `inventorySetQuantities` (40 each at *Shop location*; needs `@idempotent(key:)` and `changeFromQuantity`).
About / FAQ menu items are added once those pages exist. Section 4 (store name, currency) is still open.

Theme code reads these by handle, so the handles must match exactly.

## 1. Collections — Products → Collections → Add collection
Under **Sources**: Add condition → **Tag** → *is* → pick the tag. Save. Check the URL handle under *Search engine listing*.

| Title | Handle | Tag |
|---|---|---|
| Cleansers | `cleansers` | `cleanser` |
| Serums | `serums` | `serum` |
| Moisturisers | `moisturisers` | `moisturiser` |
| Masks | `masks` | `mask` |
| Sets | `sets` | `set` |
| Bestsellers | `bestsellers` | `bestseller` |
| Hydration | `concern-hydration` | `concern-hydration` |
| Brightening | `concern-brightening` | `concern-brightening` |
| Anti-ageing | `concern-anti-ageing` | `concern-anti-ageing` |
| Sensitive skin | `concern-sensitive` | `concern-sensitive` |

For the four concern collections, edit the handle (it defaults to the title).

## 2. Main menu — Content → Menus → Main menu
Delete Home / Catalog / Contact, then add and drag to nest:
```
Shop                         → link: Collections (all)
  By category                → link: Collections (all)
    Cleansers, Serums, Moisturisers, Masks, Sets   → each its collection
  By concern                 → link: Collections (all)
    Hydration, Brightening, Anti-ageing, Sensitive skin → each its collection
Bestsellers                  → Bestsellers collection
About                        → (page, created in Phase 3 — skip for now)
Journal                      → Blog posts › News
FAQ                          → (page, created in Phase 4 — skip for now)
```

## 3. Stock — Products → Inventory
Filter by vendor **Nova Skin**, set **Available = 40** on all 17 variants, Save.
(The CSV import ignored the stock column, so every variant was 0 and add to cart would fail.)

## 4. Settings → General
- Store name: `Nova Skin`
- Store currency: decide USD vs ₱ before Phase 3 shows prices (CSV prices were written as USD).

## 5. Optional cleanup
Archive the generated snowboard / ski-wax test products (Products → select → Archive) so they don't appear in the shop.
