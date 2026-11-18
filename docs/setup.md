# Phase 2 — Development environment

Checked on this machine (2026-11-18): **Node 22.15.1** ✅ (CLI needs ≥ 22.12) · **npm 10.9.2** ✅ · **Git 2.49** ✅ (needs ≥ 2.28) · **VS Code 1.146** ✅ · **Shopify CLI 5.0.2** ✅ (installed 2026-11-18; open a new terminal so `shopify` is on PATH) · Skeleton theme scaffolded (v1.0.0, MIT, see `LICENSE-skeleton.md`) · git initialised on `main`, nothing committed · **GitHub CLI (`gh`)** ❌ not installed (optional).
Source of the requirements: shopify.dev → Shopify CLI → Installation.

## How the pieces fit
```
VS Code (edit Liquid)  →  Shopify CLI `theme dev`  →  dev store (renders your local files, preview at http://127.0.0.1:9292)
        │                                                   ▲
        └── Git → GitHub repo ── Shopify GitHub integration ┘ (later: a branch auto-syncs to a store theme)
```
Shopify renders Liquid on their servers. `theme dev` uploads your files to a hidden **development theme** on the store and hot-reloads the preview. You never run Liquid locally.

---

## Step 1 — Shopify account + development store (you do this; I can't create accounts)
Shopify now manages dev stores from the **Dev Dashboard**.
1. Go to https://dev.shopify.com/dashboard/ and sign up or log in (free).
2. **Stores** → **Create store** → type **Dev**.
3. Name: `nova-skin-dev` (or similar). This becomes `<name>.myshopify.com`.
4. Pick a plan (any. Dev stores are free).
5. Tick **Generate test data for store**. It's optional but handy, and we'll replace it with Nova products in Step 6.
6. **Create store**. You are the owner.

Why a dev store: it's free, password-protected, doesn't expire while you build, and supports test checkout. It's a sandbox, not a live shop.
(The CLI can also do this: `shopify store create dev`.)

## Step 2 — Install Shopify CLI
```bash
npm install -g @shopify/cli@latest
```
Check it:
```bash
shopify version
```
`-g` installs it once for the whole machine, not into the project. The theme itself has **no npm dependencies**, so there's no `package.json`.
Optional: `npm config` stays untouched. To turn off CLI usage analytics, set `SHOPIFY_CLI_NO_ANALYTICS=1`.

## Step 3 — VS Code extension
Install **Shopify Liquid** (publisher: Shopify). It gives you Liquid syntax, autocomplete for objects and filters, schema validation, and **Theme Check** linting as you type.

## Step 4 — Scaffold the theme
`shopify theme init` copies Shopify's **Skeleton theme** (github.com/Shopify/skeleton-theme). Our `nova-skin/` folder already holds docs, so we init into a temp folder and move the files in:
```bash
cd D:\Ai-Web
shopify theme init nova-skin-tmp
```
Then move everything except `.git/` from `nova-skin-tmp/` into `nova-skin/` and delete the temp folder. I'll do this step.

## Step 5 — Run it against your store
```bash
cd D:\Ai-Web\nova-skin
shopify theme dev --store nova-skin-dev
```
- The first run opens a browser to log in (store owner works).
- Preview: http://127.0.0.1:9292 (Shopify says to use **Chrome**). The terminal also prints a theme-editor link and a shareable preview link.
- The store is remembered, so later runs can drop `--store`.
- Other commands you'll use: `shopify theme check` (lint), `shopify theme push --unpublished` (upload as a new theme), `shopify theme list`, `shopify auth logout` (this also deletes the dev theme).

## Step 6 — Sample catalogue (Phase 2 close-out)
`data/products.csv` holds 9 sample products (17 variant rows, Size option) using Shopify's current CSV headers. No images yet; photos come in Phase 3.
1. **First**, create the 3 metafield definitions in **Settings → Custom data → Products → Add definition**, each type *Multi-line text*:
   `Ingredients` → `custom.ingredients` · `Key benefits` → `custom.key_benefits` · `How to use` → `custom.how_to_use`.
   (Defining them first means the CSV columns land in typed fields that the theme and the editor can see.)
2. **Products → Import** → `data/products.csv`.
3. Create the collections from `docs/architecture.md §3`. Category and concern collections are *automated* with the condition "Product tag is equal to" `serum`, `concern-hydration`, and so on. Bestsellers uses the tag `bestseller`.

## Step 7 — Git + GitHub
```bash
cd D:\Ai-Web\nova-skin
git init -b main
git add .
git commit -m "Scaffold NOVA SKIN theme from Skeleton"
```
On GitHub: create an empty repo `nova-skin` (public, so it can go in your portfolio; no README, since we have one). Then:
```bash
git remote add origin https://github.com/<you>/nova-skin.git
git push -u origin main
```
Later (Phase 3+): Shopify admin → **Online Store → Themes → Add theme → Connect from GitHub** → pick `main`. Shopify then keeps a store theme in sync with that branch, both ways.

**What never goes in Git:** `.env`, Theme Access passwords, API tokens. `.gitignore` covers `.env*`, `node_modules/` and `.shopify/`.

## Troubleshooting
| Symptom | Fix |
|---|---|
| `shopify` not recognised after install | Close and reopen the terminal (PATH refresh). Check `npm config get prefix` is on PATH. |
| PowerShell: "running scripts is disabled" | Run it from Git Bash or cmd, or ask before changing the execution policy (it's a system setting). |
| `theme dev` 401 / wrong store | `shopify auth logout`, then rerun with `--store`. |
| Preview shows the old theme | It previews the *development* theme. Use the printed link, not the store's public URL. |
| "Theme directory structure invalid" | Run from the folder that contains `layout/`, `templates/` and so on. |
| Newsletter / contact form shows "Verifying your connection…" or 403 on `127.0.0.1:9292` | Shopify's bot protection challenges form POSTs relayed by `theme dev`; the challenge can't complete through the local proxy. Test forms on the store preview link (`https://<store>.myshopify.com/?preview_theme_id=<id>`, printed by `theme dev`). Not a theme bug. |
