# AniFold

AniFold is a small web page that lets you look up anime and download their covers and details as ready-made images. You can search for a title, or pick a season (for example *Fall 2026*) and download the whole season at once.

**Live demo:** https://aniifold.netlify.app/

> Arabic version: [README.ar.md](README.ar.md)

---

## What it does

- **Search** anime by name. Results show the cover and a button to add the title to a cart.
- **Season download.** Choose a season and a year (or "Current Season") and the page builds:
  - one folder per title with the cover and a **details image** (names, genres, synopsis),
  - a **covers collage** of the whole season,
  - a **chart sheet**: one big image with a card for every title (cover, studio, source, start date, short synopsis),
  - everything packed into one **ZIP** file.
- **Single outputs.** You can also download only the chart image or only the covers collage, without a ZIP.
- **Cart.** Collect titles from searches, then download the selection as a ZIP, a covers collage, or a details collage.
- **Details window.** Click a result to see the cover, names, genres and synopsis, and download the folder (ZIP), the cover only, or the synopsis image only.
- **Synopsis translation** into Arabic, French, Spanish, Turkish, German, Russian, Portuguese, Indonesian, Japanese and Chinese. The original text is kept if translation fails.
- **Image quality** choice (Standard / High / Max) before each download.
- **Progress and control.** Overall and per-item progress bars, an activity log, and a Stop button on every running action.
- **Light and dark theme**, saved in the browser.

Each download asks for its options first (synopsis language and/or image quality), and remembers your last choice.

---

## How it works

AniFold has **no backend**. Everything runs in the browser from a single `index.html` file.

**Data sources** (used in this order, the next one only if the previous fails):

| Use | Sources |
|---|---|
| Season lists | AniList (GraphQL) → Kitsu → Jikan (MyAnimeList) |
| Search | AniList → Kitsu → Jikan |

**Images** are downloaded directly first. If the image server blocks the request (CORS), the page retries through public CORS proxies, and as a last resort through an image proxy. The route that worked is remembered per website.

**Translation** tries these engines in order: the translator built into Chrome (when available), Google Translate, Google Translate through a proxy, and MyMemory. Several synopses are sent together in numbered batches to reduce the number of requests; if the numbering comes back damaged, the batch is split and retried.

**Drawing.** The details image, the covers collage and the chart sheet are drawn locally with the HTML `<canvas>`. The canvas size is limited to what browsers allow, so very high quality may be lowered automatically on large seasons.

**Saved in your browser (`localStorage`):**

| Key | Content |
|---|---|
| `anifold-cart:v1` | the cart |
| `anifold-opts:v1` | last language / quality choice |
| `ui-theme` | light or dark |
| `anime-season:*`, `anime-tr:*` | temporary caches (season lists, translations) |

The temporary caches are removed after each operation finishes. The cart and the theme are never removed.

---

## Built with

- HTML, CSS and plain JavaScript (no framework, no build step, no package manager)
- [JSZip](https://stuk.github.io/jszip/) for creating ZIP files (loaded from cdnjs, with jsDelivr as a back-up)
- Browser APIs: `fetch`, `canvas`, `localStorage`, `<dialog>`, `AbortController`
- Public APIs: AniList, Kitsu, Jikan, Google Translate, MyMemory, and the Translator API of Chrome when present
- Hosted on Netlify as a static page

---

## How it was developed

The project started as a single-page tool and was improved step by step:

1. **Core features:** season lists, ZIP creation, details image, chart sheet, translation.
2. **Search and cart:** search with fallback sources, the details window, a saved cart.
3. **Interface:** custom icons added for every action, a redesigned layout, light/dark theme, light animations (with a reduced mode for weak devices and for users who turned animations off).
4. **Download options:** a popup for language and quality, a covers-only collage mode, and a Stop button that appears on the running action.
5. **Clean-up:** unused code removed, the code rewritten with full names and split into labelled sections, and comparison tests run against the previous version (computed styles, downloaded files, and behavior) to make sure nothing changed.

The code was written with the help of an AI assistant ([Claude](https://www.anthropic.com/claude)) under the author's direction and review.

---

## Project structure

```
index.html     the whole project: styles, page, and script
```

Inside `index.html` the script is divided into numbered sections (settings, icons, network, season sources, image drawing, translation, saved data, search, dialogs, main download, start-up). The styles are divided in the same way.

---

## Run locally

No installation is needed. Open `index.html` in a modern browser (Chrome, Edge, Firefox or Safari) with an internet connection.

To use another Jikan server, or change timeouts, the number of parallel downloads, or the blocked genres, edit the `SETTINGS` section at the top of the script.

---

## Notes and limits

- The page depends on **free public services**. They can be slow, rate-limited, or unavailable. The Jikan public instance was announced to shut down in October 2026; the address can be changed in `SETTINGS`.
- Free CORS proxies are used only when needed and may fail at any time.
- The covers collage uses at most 300 covers.
- Titles with these genres are hidden and never downloaded: hentai, yaoi, yuri, boys love, girls love, erotica.
- The Chrome translator works only in versions that include it and have the language model installed.
- Covers and synopses belong to their owners. Use downloaded files for personal use.

---

## Author

AmrOdhymi
