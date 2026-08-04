# image-carousel

HTML templates for generating LinkedIn/Instagram carousel slides (square images, 1080×1080px). This repo is designed to be used by an AI agent that fills in text and renders each slide to a PNG image.

## What's in this repo

```
image-carousel/
├── templates.json          ← READ THIS FIRST. Machine-readable index of everything below.
├── editorial-classic/      ← one theme = one folder
│   ├── 01-cover.html       ← slide 1: title/hook
│   ├── 02-content.html     ← slide 2: title + 3 paragraphs
│   ├── 02-content-list.html← alternative slide 2: title + 3 numbered points
│   └── 03-closing.html     ← last slide: CTA ("follow & save")
├── urban-bold/              (same 4 files)
├── tech-grid/                (same 4 files)
├── calm-editorial/           (same 4 files)
├── noir-quote/                (same 4 files)
├── botanical/                 (same 4 files)
├── mystic-minimal/             (same 4 files)
├── bold-statement/              (same 4 files)
├── retro-archive/                (same 4 files)
└── fashion-diagonal/              (same 4 files)
```

10 themes total, 4 HTML files each = 40 files. Every theme has the exact same 4-file structure, just different colors/fonts/layout. Pick ONE theme per carousel post and use its files consistently — do not mix files from different themes in the same carousel.

## Step-by-step: how to generate a carousel

1. **Read `templates.json`**. It lists every theme, every file, every placeholder token, and the max character count each placeholder can safely hold.
2. **Pick a theme** based on the topic (see the theme table below, or the `best_for` field in `templates.json`).
3. **Decide how many slides** you need (minimum 2: cover + closing; typical: cover + 1-2 content slides + closing).
4. **Write your copy** in Indonesian (or whatever language the post needs), keeping each piece of text at or under its `max_chars` limit from `templates.json`.
5. **Replace placeholders**: open the HTML file as plain text and replace every `{{TOKEN}}` with your text — literal find-and-replace, nothing fancier.
6. **Save each filled file**, then render it: open the file in a headless browser at viewport 1080×1080 and screenshot the `<body>` element. That produces one PNG per slide.
7. **Order the PNGs**: `01-cover` → `02-content` (or `02-content-list`) → `03-closing`. That sequence is what gets uploaded as the LinkedIn/IG carousel.

## Placeholder tokens (exact list)

Every `{{TOKEN}}` appears **exactly once** per file, so a simple string replace is always safe — no regex needed, no risk of double-replacing.

| File type | Tokens present | What each token means |
|---|---|---|
| `01-cover.html` | `{{TITLE}}` | The big headline on the cover slide |
| `02-content.html` | `{{TITLE}}`, `{{BODY_1}}`, `{{BODY_2}}`, `{{BODY_3}}` | A sub-heading + 3 paragraphs |
| `02-content-list.html` | `{{TITLE}}`, `{{ITEM_1}}`, `{{ITEM_2}}`, `{{ITEM_3}}` | A sub-heading + 3 numbered/bulleted points |
| `03-closing.html` | `{{TITLE}}`, `{{SUBTITLE}}` | A short closing line + a one-sentence CTA |

**Use either `02-content.html` OR `02-content-list.html`, not both** — they are two alternative designs for the same slide position (paragraph style vs. numbered-list style). Pick whichever fits your content better.

### Minimal working example

```python
# pseudocode — the actual replace can be done in any language
html = read_file("editorial-classic/01-cover.html")
html = html.replace("{{TITLE}}", "5 Tren AI di Industri Tahun Ini")
write_file(html)
# then render this file to a 1080x1080 PNG
```

## Character limits — do not skip this

Every placeholder has a `max_chars` value in `templates.json`. These are **not enforced by the browser** — they are a design guideline computed from each theme's font size and text box width. If you write text longer than `max_chars`:

- The text will still render (nothing crashes),
- But it will **visually fade out at the bottom edge** of its container (a built-in CSS overflow guard),
- Which means part of your text becomes invisible in the final image.

**Rule: always keep your generated text at or under `max_chars`.** If a topic genuinely needs more room, switch to a theme with a higher budget (see table below) instead of writing over the limit.

## Theme picker

| Theme | Best for | Text capacity |
|---|---|---|
| `editorial-classic` | Neutral, dense, safe default for any tech/industry topic | High |
| `tech-grid` | Data, business, structured listicles | High |
| `noir-quote` | Quotes, executive statements, storytelling | High |
| `retro-archive` | Research findings, "did you know", documented facts | High |
| `botanical` | Sustainability, personal growth, culture/people topics | Medium |
| `urban-bold` | Attention-grabbing hooks, short punchy titles | Medium |
| `fashion-diagonal` | Personal branding, career topics | Medium |
| `calm-editorial` | Soft skills, mindset, non-aggressive tone | Low |
| `bold-statement` | ONE strong headline per slide — not for dense content | Low |
| `mystic-minimal` | Very short elegant one-liners only — lowest capacity of all | Lowest |

If unsure, default to **`editorial-classic`** or **`tech-grid`** — they hold the most text and have the highest color-contrast (easiest to read).

## Fixed elements you never need to touch

Every file already has, hardcoded and correctly positioned:
- The page number (01, 02, ✓)
- The LinkedIn logo + `@jefry-chiedi` handle watermark
- All colors, fonts, and layout/spacing

You only ever edit the `{{TOKEN}}` values. Do not change any CSS, any HTML structure, or the watermark/handle text.

## Design rules already baked into every template (for context, not action)

These were already fixed during development — you don't need to redo this work, just know it's handled:
- All 10 themes pass WCAG AA text contrast (≥4.5:1) against their background.
- No theme uses font-weight lighter than 400 for body text (thin fonts were fixed).
- Titles are positioned consistently regardless of text length (no "floating" decorations).
- Every text box has the fade-guard described above.

## Canvas spec

- Size: exactly **1080×1080px** (1:1 ratio, required for LinkedIn/IG carousels).
- Render target: screenshot the `<body>` element only, at 1080×1080 viewport, no browser chrome.
