# Reborn Worldwide — Shopify Theme

## Stack
- **Base**: Shopify Dawn 15.3.0 (heavily modified)
- **Language**: Liquid + Vanilla JS + CSS (no frameworks)

## Directory structure
```
assets/        CSS, JS, images — reference via {{ 'file.css' | asset_url | stylesheet_tag }}
config/        settings_schema.json + settings_data.json
layout/        theme.liquid (main), password.liquid, ecom.liquid
locales/       en.default.json + en.default.schema.json for translations
sections/      .liquid files — all section logic lives here
snippets/      Reusable Liquid partials rendered with {% render %}
templates/     JSON templates (index.json, product.json, etc.)
```

## JSON template rules
- Max **25 sections** per template, max **50 blocks** per section
- Section IDs must be unique within the file — never duplicate
- Section `type` must match the filename of a `.liquid` file in `sections/`
- Disabled sections: add `"disabled": true` to keep them in the file without rendering
- `order` array controls render sequence — only listed sections render
- Do NOT hand-edit `custom_css` keys; those are merchant-controlled via the editor
- Comments and trailing commas are **not** preserved in `templates/*.json` — keep valid JSON only
- The `image_banner_fgQaCM` and `image_banner_DQiLYF` entries are intentionally kept as disabled for admin reference — do not delete them

## Section development conventions
- Every section must have a `{% schema %}` block with `name`, `settings`, and `presets`
- Sections without presets cannot be added or removed via the theme editor — always include at least one preset
- Use `enabled_on` / `disabled_on` to restrict sections to appropriate templates (e.g., announcement-bar is header-only)
- Section settings reference: use `section.settings.<id>` in Liquid
- Block settings reference: use `block.settings.<id>` inside a `{% for block in section.blocks %}` loop
- Use `{{ section.id }}` for unique CSS class scoping, e.g. `.section-{{ section.id }}-padding`
- Section-scoped CSS goes inside `{% stylesheet %}` tags; section-scoped JS inside `{% javascript %}` tags

## Liquid best practices
- Use `{% render %}` (not `{% include %}`) for snippets — scoped and faster
- Use `{{ image | image_url: width: 800 | image_tag: loading: 'lazy' }}` for all product/collection images
- Above-the-fold images: use `loading: 'eager'` and `preload: true`
- Money output: always use `{{ product.price | money }}` — never format manually
- Avoid logic inside loops: assign variables before `{% for %}` blocks
- Translations in schemas: always use `t:` keys from `locales/*.schema.json`, not hardcoded strings
- Translations in storefront Liquid: use `{{ 'key' | t }}` from `locales/en.default.json`

## Performance rules
- Keep minified JS per section under **16 KB**
- No React, Vue, Angular — use native browser APIs and Web Components
- All `<script>` tags must use `defer` or `async`
- Images below the fold: `loading="lazy"` via `image_tag` filter
- CSS must stay within its own file to benefit from Shopify's automatic CSS subsetting

## Color schemes

### CSS design tokens (`:root`)

| Variable        | Value                            | Role                                 |
|-----------------|----------------------------------|--------------------------------------|
| `--ink`         | `#0E0E0E`                        | Primary text, borders, buttons       |
| `--bone`        | `#EDE7DA`                        | Page background (default)            |
| `--bone-2`      | `#E2DBC9`                        | Placeholder fills, input backgrounds |
| `--paper`       | `#F6F1E4`                        | Elevated surfaces (cards, sidebars)  |
| `--orange`      | `oklch(70% 0.18 50)` ≈ `#F26B1F` | Primary accent                       |
| `--orange-deep` | `oklch(58% 0.18 45)`             | Hover / meta accent                  |
| `--acid`        | `oklch(89% 0.19 110)`            | Reserved yellow-green                |
| `--line`        | `rgba(14,14,14,.18)`             | Dividers                             |
| `--muted`       | `rgba(14,14,14,.62)`             | Secondary text                       |

### Tweaks palettes

| Name           | Background | Ink       | Accent    |
|----------------|------------|-----------|-----------|
| Bone (default) | `#EDE7DA`  | `#0E0E0E` | `#F26B1F` |
| Dark           | `#0E0E0E`  | `#EDE7DA` | `#DCEC3D` |
| Red            | `#F2EFE7`  | `#1A1A1A` | `#FF4D2E` |
| Forest         | `#E8E2D2`  | `#0A1F1A` | `#7BAF2C` |

### Shopify scheme mapping

| Design role             | Shopify scheme          |
|-------------------------|-------------------------|
| Bone surfaces (default) | `scheme-1` / `scheme-5` |
| Ink dark sections       | `scheme-4`              |
| Paper cards             | `scheme-2`              |
| Pure white              | `scheme-3`              |
| `--orange` accent       | Button color per scheme |

### Rules
- Use `color_scheme` setting type referencing Dawn's `color_scheme_group` — never hardcode hex values
- In Liquid/CSS, reference Dawn tokens: `rgb(var(--color-foreground))` (ink), `rgb(var(--color-background))` (bone), `rgba(var(--color-button), 1)` (orange)
- Use design token names (`--ink`, `--bone`, `--orange`) only in prototype/design references — in theme code always use the Dawn CSS variable equivalents above
- `--orange` accent must always be set as the button color in the scheme, not hardcoded inline

## Template alternates
Product pages have per-chapter alternates:
- `product.ecom-reborn-product-my-swamp.json`
- `product.ecom-reborn-product-monsters-drink.json`
- `product.ecom-reborn-product-anime-fan.json`
- `product.ecom-reborn-product-bottom-badass.json`

When adding new product chapters, create a matching alternate template.

## Accessibility requirements
- All images need descriptive `alt` text; decorative images use `alt=""`
- Form fields require `<label>` with matching `for` attribute
- Use `<nav>`, `<main>`, `<section>`, `<header>`, `<footer>` — not generic `<div>` wrappers for layout landmarks
- Interactive elements must be keyboard-navigable; focus indicators must be visible
- Touch targets minimum 44×44px
- Color contrast: 4.5:1 for body text (<24px), 3.0:1 for large text

## Code quality
- Delete redundant code and unused variables, classes, settings, and blocks — cleanup is encouraged
- Remove disabled sections from JSON templates if no longer needed (exception: `image_banner_fgQaCM` and `image_banner_DQiLYF` are kept intentionally for admin reference)
- Prefer editing existing files over creating new ones

## What NOT to do
- Do not use `{% include %}` — deprecated, use `{% render %}`
- Do not add React/Vue/Angular or any heavy JS framework
- Do not hardcode prices, shipping costs, or currency symbols — use Liquid money filters
- Do not add `custom_css` keys manually to JSON templates — merchant-controlled only
- Do not commit `config/settings_data.json` changes unless intentional — it contains live merchant settings
- Do not use `parser-blocking` scripts (no `<script>` without `defer`/`async`)
- Do not duplicate section IDs in the same template file
