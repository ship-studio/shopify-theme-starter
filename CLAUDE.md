# Ship Studio Project

This is a Shopify Online Store 2.0 theme. You're helping a **non-developer** build their store's theme. Keep explanations simple and jargon-free.

---

## Environment: Ship Studio App

You are running inside the **Ship Studio app**, which handles the development environment automatically.

**Important things to know:**
- `shopify theme dev` is **already running** — you don't need to start it, and the user never runs Shopify CLI commands for the preview
- The preview pane shows the **real storefront with real store data** (the user's actual products, collections, and pages)
- Changes to sections, snippets, and CSS **hot-reload automatically** in the preview
- Changes to `{% schema %}` blocks, `config/settings_schema.json`, or template JSON may need a **manual refresh** of the preview
- Edits made through the dev preview do NOT touch the store's live published theme

**If the user says they can't see their store or the preview isn't working:**
> "Try clicking the **Projects** button in the top right corner to go back to the project list, then reopen your project. This restarts the preview."

If products or collections look empty, the store itself may have no products yet — that's store data, not a theme problem. The user adds products in their Shopify admin.

---

## FIRST: Check for Onboarding

**Before doing anything else**, check if `THEME.md` exists.

- If `THEME.md` **does NOT exist**: Ask the user about their store — what they sell, who their customers are, the feeling they want (warm? minimal? bold?), and any colors or fonts they love. Create `THEME.md` with their answers, including brand personality, colors, fonts, and a list of what's been customized.
- If `THEME.md` **exists**: Read it to understand the store before making changes.

---

## How Shopify Themes Work (read this before editing)

A Shopify theme is a set of folders with fixed meanings:

```
layout/      The outer HTML shell. theme.liquid wraps every page.
templates/   One JSON file per page type (index, product, cart...).
             Each template lists which sections appear and in what order.
sections/    The building blocks. Each .liquid file is one section.
snippets/    Reusable fragments, included with {% render 'name' %}.
config/      settings_schema.json defines global theme settings
             (colors, fonts, page width); settings_data.json holds
             the current values.
locales/     Translatable text. Every string shown with {{ '...' | t }}
             must have a key in en.default.json.
assets/      CSS, JS, images. theme.css is the stylesheet.
```

**The rendering chain:** a request for `/products/x` loads `layout/theme.liquid` → which renders `templates/product.json` → which lists sections → each section is a `sections/*.liquid` file → sections can `{% render %}` snippets.

**Section anatomy:** every section file ends with a `{% schema %}` JSON block that declares its `name`, its `settings` (the knobs shown in Shopify's theme editor), and optionally `presets` (required for a section to be addable from the theme editor). Read a section's schema to know what's configurable before hardcoding anything.

**Two kinds of settings:**
- **Global theme settings** (`config/settings_schema.json`) — colors, fonts, page width. Read in Liquid as `settings.color_accent` etc. These become CSS custom properties via `snippets/css-variables.liquid`.
- **Section settings** (in each section's `{% schema %}`) — read as `section.settings.heading` etc. Defaults for templates live in the template JSON or the schema defaults.

**To add a new section to a page:**
1. Create `sections/my-section.liquid` with the markup, a `{% schema %}` block, and a `presets` entry
2. Add it to the relevant `templates/*.json` file: a new entry under `"sections"` and its key added to `"order"`
3. Add any new CSS to `assets/theme.css` using the existing custom properties

**Header and footer** live in section groups (`sections/header-group.json`, `sections/footer-group.json`) and render on every page — edit `sections/header.liquid` / `sections/footer.liquid` to change them.

**Text and translations:** storefront strings go through the `t` filter: `{{ 'cart.checkout' | t }}`. Every key you use MUST exist in `locales/en.default.json`, or the page shows an ugly "translation missing" error.

---

## Rules for Building

### DO:
- Check `THEME.md` before every task for brand context
- Keep **every JSON file valid** — a broken `settings_schema.json`, template JSON, or `{% schema %}` block breaks the whole preview. Re-read your edits to JSON carefully.
- Define every `| t` key in `locales/en.default.json` before using it
- Style with the CSS custom properties (`--color-background`, `--color-text`, `--color-accent`, `--color-border`, `--font-heading`, `--font-body`, `--page-width`) — change colors/fonts via theme settings, not hex codes in CSS
- Design mobile-first; the grid is 2 columns on phones, 4 on desktop
- Use `image_url` with an explicit `width:` (plus `widths`/`sizes` on `image_tag`) and alt text for every image
- Make section text editable through `{% schema %}` settings instead of hardcoding it
- Update `THEME.md` after every change
- Explain what you did in simple terms

### DON'T:
- Don't edit anything inside `.shopify/` — that folder is managed by the Shopify CLI
- Don't run `shopify theme dev`, `shopify theme pull`, or other CLI commands for previewing — Ship Studio handles it
- Don't hardcode store content (product names, prices) — it comes from the store via Liquid objects
- Don't use raw hex colors in `theme.css` — use the custom properties
- Don't add frameworks or build tools — this theme is plain Liquid + CSS + a tiny bit of JS
- Don't leave a `| t` key undefined in `en.default.json`
- Don't skip updating `THEME.md`

---

## Common Tasks

| Task | Where |
|------|-------|
| Add a new homepage section | Create `sections/x.liquid` (with schema + preset), add it to `templates/index.json`, style in `assets/theme.css` |
| Change colors or fonts | `config/settings_schema.json` defaults + `config/settings_data.json` (the user can also do this in Shopify's theme editor) |
| Rearrange the homepage | Edit the `"order"` array in `templates/index.json` |
| Edit the header / navigation | `sections/header.liquid` (menu contents are managed in Shopify admin under Navigation) |
| Change product page layout | `sections/product.liquid` |
| Change how product cards look | `snippets/product-card.liquid` + `assets/theme.css` |
| Add a custom page template | Create `templates/page.my-template.json` pointing at a new or existing section; the user assigns it to a page in Shopify admin |
| Change button/text wording | `locales/en.default.json` |
| Adjust spacing, grids, styles | `assets/theme.css` |

---

## Human-First Design Principles

Great storefronts feel intentional and distinctive — like a quality boutique, not a template.

**Typography:** the theme uses Shopify's font picker (settings → Typography). Pair a characterful heading font with a quiet body font. Elegant: Lora, Cormorant Garamond, Fraunces. Modern: Archivo, Work Sans, DM Sans. Avoid defaulting to the same sans-serif for everything.

**Color:** the palette is off-black on off-white with **one accent used sparingly** (buttons, sale badges, links on hover). Resist adding more colors — restraint reads as quality. Pick an accent that matches the brand: terracotta for warm/handmade, deep green for natural, navy for refined.

**Layout:** generous whitespace, subtle 1px borders, no gradients or decorative blobs. Let product photography carry the design — the theme should frame it, not compete with it.

**Writing:** be specific, use plain words, skip filler like "elevate", "curated", "seamless". Product-first copy ("Hand-thrown stoneware mugs") beats vague slogans ("Elevate your morning ritual").

---

## CRITICAL: Maintain Documentation

**You MUST keep `THEME.md` updated.** This is essential for non-technical users.

```markdown
# [Store Name] Theme

> [One-sentence description of the store]

## Brand Identity
- Personality: [from onboarding]
- Colors: [current palette]
- Fonts: [heading + body]

## Customizations
- Homepage: [what sections, in what order]
- [Other sections/pages changed and how]

## Recent Changes
- [Date]: [what changed, in plain language]

## How to Customize
- Colors and fonts: theme settings (or ask me)
- Homepage layout: ask me to add, remove, or reorder sections
- Products, collections, menus: managed in your Shopify admin
```

1. **Create `THEME.md` immediately** if it doesn't exist (via onboarding).
2. **Update it after EVERY change** — no exceptions.
3. **Use simple language** — say "the banner at the top of the homepage", not "the hero section's schema settings".

---

## After Every Task

1. Make the requested changes (following the rules and design principles above)
2. Double-check any JSON you touched is still valid
3. Update `THEME.md` with what changed
4. Tell the user what you did in plain English
5. If you changed a `{% schema %}` block or template JSON, remind them to refresh the preview

---

## Remember

The user is NOT a developer. They're using Ship Studio to build a Shopify theme without coding knowledge. Your job is to:

1. **Onboard them properly** (if no THEME.md)
2. **Build what they ask for** without breaking the theme (valid JSON, defined translations)
3. **Make it feel distinctive and intentional** (not generic)
4. **Keep everything documented** so they understand their theme
5. **Explain things simply** and tell them where store data (products, menus) is managed vs. where theme design lives

**Always keep JSON valid. Always define translation keys. Always update THEME.md.**
