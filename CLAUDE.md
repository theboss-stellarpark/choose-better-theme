# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Development Commands

```bash
# Start local dev server (syncs file changes to Shopify draft theme in real time)
shopify theme dev

# Push local files to the live Shopify theme
shopify theme push

# Pull current theme files from Shopify
shopify theme pull
```

## Theme Architecture

This is a **Shopify Horizon v3.5.1** theme customized for the **Choose Better** brand.

### Key Config Files

- `templates/index.json` — homepage layout: all sections, blocks, settings, and render order. This is the primary file for homepage customization.
- `sections/header-group.json` — header layout and settings (nav, logo, scheme)
- `sections/footer-group.json` — footer layout: 4-column navy layout (brand/social, SHOP menu, COMPANY menu, newsletter)
- `config/settings_data.json` — global theme settings including all color schemes. Custom schemes added: `scheme-white` (pure white, brand colors), `scheme-navy-header` (dark navy `#0f1a26`)

### Block System

Horizon uses a nested block architecture. Blocks live in `blocks/` and are referenced by `type` in JSON templates:

- **Public blocks** (user-configurable): `text`, `image`, `button`, `icon`, `group`, `menu`, `social-links`, `email-signup`, `logo`, `swatches`, `custom-liquid`, etc.
- **Internal blocks** (prefixed with `_`): `_product-card`, `_product-list-content`, `_product-list-button`, `_divider`, `_content-without-appearance`, `_media-without-appearance`, etc. These are section-specific and cannot be added freely.

When `type_preset` is set to anything other than `"custom"` (e.g. `"h4"`, `"rte"`, `"paragraph"`), the font/size overrides in the block settings are **ignored**. Use `"type_preset": "custom"` to apply custom `font`, `font_size`, `letter_spacing`, etc.

### Font CSS Variables

Defined in `snippets/theme-styles-variables.liquid`:
- `var(--font-body--family)` — Poppins 400 (regular)
- `var(--font-subheading--family)` — Poppins 600 (semi-bold)
- `var(--font-heading--family)` — Poppins 500 (medium)

### Color Palette

Defined as color schemes in `config/settings_data.json`:
- `#f3efe7` — cream (scheme-1 background, values strip, UGC section)
- `#ffffff` — white (scheme-white, Best Sellers section)
- `#1c2a39` — dark navy (foreground text, buttons)
- `#0f1a26` — header navy (scheme-navy-header, header + footer)
- `#c2a35d` / `#c9a646` — gold (accent, icons, star, decorative lines)
- `#8b8f94` — mid gray (borders, muted text)

### Layout Sections (non-homepage)

Layout sections rendered globally via `layout/theme.liquid`:
- Header: `{% sections 'header-group' %}` → `sections/header-group.json`
- Footer: `{% sections 'footer-group' %}` → `sections/footer-group.json`

These are NOT in `templates/index.json`.

### CSS in This Theme

Styles are written in `{% stylesheet %}` blocks inside `.liquid` files — they compile into a global CSS bundle. Avoid using `#shopify-section-{key}` selectors to scope styles; JSON template section IDs use a different format. Instead scope by block class or use inline styles.

### Section Alignment — Column vs Row Direction

For `group` and `section` blocks, alignment settings differ by direction:
- **Row direction**: use `horizontal_alignment` and `vertical_alignment`
- **Column direction**: use `horizontal_alignment_flex_direction_column` and `vertical_alignment_flex_direction_column`

### Divider Behavior

`snippets/divider.liquid` has custom CSS additions:
- Direct children of `.layout-panel-flex--row`: renders as a **vertical** line (for values strip column separators)
- Nested row dividers (`.layout-panel-flex--row .layout-panel-flex--row > .divider`): renders as **horizontal** expanding lines with `:first-child` right-aligned and `:last-child` left-aligned (used for the ——★—— decoration in Best Sellers)

### Homepage Section Order

`templates/index.json` order:
1. `hero_jVaWmY` — hero with lifestyle photo + headline
2. `values_strip` — 4-column values with gold icons and vertical dividers
3. `product_list_fa6P9H` — Best Sellers (5 columns, `scheme-white`, points to `all` collection — change to `best-sellers` when ready)
4. `media_with_content_Rr4PTY` — Brand Story split (left photo, right cream content, CB watermark)
5. `ugc_section` — UGC grid ("REAL PEOPLE. REAL CHOICES." + 6 portrait photos)

### Footer Nav Menus Required

Two navigation menus must be created in **Shopify Admin → Online Store → Navigation**:
- Handle `footer-shop`: All Products, Hoodies, T-Shirts, Accessories, Best Sellers
- Handle `footer-company`: About Us, Our Mission, Journal, Contact, FAQ
