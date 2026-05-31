---
description: 'Use when working on the Reborn Worldwide Shopify theme — Liquid sections, JSON templates, CSS, JS, snippets, or schema files. Covers Dawn conventions, section rules, naming, performance, and what not to touch.'
applyTo: '**/*.liquid,templates/**/*.json,sections/**,snippets/**,assets/**,locales/**'
---

# Reborn Worldwide — Shopify Theme Rules

## Stack

- Base: Shopify Dawn 15.3.0 (heavily modified)
- Language: Liquid + Vanilla JS + CSS — no frameworks (no React, Vue, Angular)

## Directory Structure

```
assets/     CSS, JS, images — reference via {{ 'file.css' | asset_url | stylesheet_tag }}
config/     settings_schema.json + settings_data.json
layout/     theme.liquid (main), password.liquid, ecom.liquid
locales/    en.default.json + en.default.schema.json for translations
sections/   .liquid files — all section logic lives here
snippets/   Reusable Liquid partials rendered with {% render %}
templates/  JSON templates (index.json, product.json, etc.)
```

## JSON Template Rules

- Max 25 sections per template, max 50 blocks per section
- Section IDs must be unique within a file — never duplicate
- Section `type` must match a `.liquid` filename in `sections/`
- `order` array controls render sequence — only listed IDs render
- Disabled sections: add `"disabled": true` (do not delete)
- No comments or trailing commas — must be valid JSON
- Do NOT hand-edit `custom_css` keys — merchant-controlled via the editor
- `image_banner_fgQaCM` and `image_banner_DQiLYF` are intentionally kept as disabled for admin reference — do not delete them

## Section Development

- Every section requires `{% schema %}` with `name`, `settings`, and at least one `presets` entry — sections without presets cannot be added via the theme editor
- Scope CSS with `{{ section.id }}`: e.g. `.section-{{ section.id }}-padding`
- Section CSS → `{% stylesheet %}` tags; section JS → `{% javascript %}` tags
- Use `enabled_on` / `disabled_on` to restrict sections to correct templates
- Reference settings as `section.settings.<id>` and block settings as `block.settings.<id>`

## Liquid

- Use `{% render %}` — never `{% include %}` (deprecated)
- Images: `{{ image | image_url: width: 800 | image_tag: loading: 'lazy' }}`
- Above-the-fold images: `loading: 'eager'` and `preload: true`
- Money: always `{{ product.price | money }}` — never format manually
- Translations in schemas: `t:` keys from `locales/*.schema.json`
- Translations in Liquid: `{{ 'key' | t }}` from `locales/en.default.json`
- Assign variables before `{% for %}` loops — avoid logic inside loops

## JavaScript & CSS

- All `<script>` tags must use `defer` or `async` — no parser-blocking scripts
- Max 16 KB minified JS per section
- CSS must stay in its own file for Shopify's automatic CSS subsetting

## Color Schemes

### CSS design tokens (`:root`)

| Variable        | Value                            | Role                                 |
| --------------- | -------------------------------- | ------------------------------------ |
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
| -------------- | ---------- | --------- | --------- |
| Bone (default) | `#EDE7DA`  | `#0E0E0E` | `#F26B1F` |
| Dark           | `#0E0E0E`  | `#EDE7DA` | `#DCEC3D` |
| Red            | `#F2EFE7`  | `#1A1A1A` | `#FF4D2E` |
| Forest         | `#E8E2D2`  | `#0A1F1A` | `#7BAF2C` |

### Shopify scheme mapping

| Design role             | Shopify scheme              |
| ----------------------- | --------------------------- |
| Bone surfaces (default) | `scheme-1` / `scheme-5`     |
| Ink dark sections       | `scheme-4`                  |
| Paper cards             | `scheme-2`                  |
| Pure white              | `scheme-3`                  |
| `--orange` accent       | Button color set per scheme |

### Rules

- Use `color_scheme` setting type referencing Dawn's `color_scheme_group` — never hardcode hex values
- In Liquid/CSS, reference Dawn tokens: `rgb(var(--color-foreground))` (ink), `rgb(var(--color-background))` (bone), `rgba(var(--color-button), 1)` (orange)
- Use design token names (`--ink`, `--bone`, `--orange`) only in prototype/design file references — in theme code always use the Dawn CSS variable equivalents above
- `--orange` accent must always be set as the button color in the scheme, not hardcoded inline

## Accessibility

- All images need `alt` text; decorative images use `alt=""`
- Form fields need `<label for="...">` matching input `id`
- Use semantic landmarks: `<nav>`, `<main>`, `<section>`, `<header>`, `<footer>`
- Touch targets minimum 44×44 px
- Color contrast: 4.5:1 body text, 3.0:1 large text
- All interactive elements keyboard-navigable with visible focus indicators

## Code Quality

- Delete redundant code and unused variables, classes, settings, and blocks — cleanup is encouraged
- Remove disabled sections from JSON templates if they are no longer needed (exception: `image_banner_fgQaCM` and `image_banner_DQiLYF` are kept intentionally for admin reference)
- Prefer editing existing files over creating new ones

## Template Alternates

Product pages have per-chapter alternates:

- `product.ecom-reborn-product-my-swamp.json`
- `product.ecom-reborn-product-monsters-drink.json`
- `product.ecom-reborn-product-anime-fan.json`
- `product.ecom-reborn-product-bottom-badass.json`

When adding new product chapters, create a matching alternate template.

## What NOT to Do

- Do not use `{% include %}` — deprecated, use `{% render %}`
- Do not add React, Vue, Angular, or any heavy JS framework
- Do not hardcode prices, shipping costs, or currency symbols — use Liquid money filters
- Do not add `custom_css` keys manually to JSON templates — merchant-controlled only
- Do not commit `config/settings_data.json` changes unless intentional — it contains live merchant settings
- Do not use parser-blocking scripts — every `<script>` must have `defer` or `async`
- Do not duplicate section IDs in the same template file
- Do not hardcode hex color values — use color scheme settings or Dawn CSS variables
