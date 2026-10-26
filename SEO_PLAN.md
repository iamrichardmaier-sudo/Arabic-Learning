# SEO Plan: BenArabic

**Goal:** show up near the top of Google when people search for Arabic words, such as "plural of book in Arabic", "كتاب جمع", "kataba conjugation" or "how to say teacher in Arabic".

**How the work splits:** I do all the coding. You make a few decisions and spend roughly 30 to 45 minutes in total, mostly one-time setup. Your tasks are marked **[YOU]**.

---

## 1. What I found

| # | Finding | Impact |
|---|---|---|
| 1 | **Every page requires a login.** `/` redirects logged-out visitors to `/auth`, so Google only ever sees a login form. | **Blocking.** Nothing can rank right now. |
| 2 | **The page title is "Lovable App"** and the description is "Lovable Generated Project". The social preview image and Twitter handle belong to Lovable. | Search results and shared links show Lovable's branding instead of yours. |
| 3 | **Pages are built in the browser.** The HTML Google downloads is an empty `<div id="root">`. Google can run JavaScript, but slowly and unreliably, and Bing and others often can't. | Slow, partial indexing even after the pages are made public. |
| 4 | No sitemap, no canonical URLs, no structured data. `lang="en"` is set but there's no Arabic language markup. The favicon is hosted on a third-party bucket. | Missing basic signals search engines use. |
| 5 | **There's useful content locked inside the app:** 244 nouns with harakat, English meaning, plural forms, plural pattern (e.g. فِعَال), grammar note, category and dialect (Fusha/Shaami). There are also 7 plural-category explainers, a conjugation engine and text-to-speech. | This is the raw material for hundreds of public pages. |
| 6 | **Bug: the conjugation engine's past tense is wrong for every verb.** It produces كَتَبَتُ instead of كَتَبْتُ and كَتَبَوا instead of كَتَبُوا. Hollow and defective verbs are wrong too: قَالَتُ instead of قُلْتُ, رَمَىتُ instead of رَمَيْتُ, and the present يَرْمِيُ should be يَرْمِي. | This already affects the in-app Verb Drill. Wrong tables on public pages would damage trust and rankings, so it must be fixed first. |
| 7 | The repo currently contains only `benarabic-main.zip`. Lovable deploys from the GitHub repo it's connected to. | I need to know which repo Lovable syncs with, or my changes won't go live (see Section 5). |

### Being realistic about "top of the list"
Broad searches like "Arabic dictionary" or "Arabic words" are dominated by Google Translate, Wiktionary, Almaany and Reverso. A new site won't beat them in the near term. What can rank is the **long tail**: thousands of specific searches where those sites give a thin or messy answer and this app can give a better one:

- "plural of [word] in Arabic" / "جمع [word]": the app's plural data is detailed and well organized.
- "[verb] conjugation" / "تصريف [verb]": full tables once the engine is fixed.
- "broken plural patterns", "sound feminine plural examples", "Levantine plurals": category guides.
- "words from the root k-t-b": root family pages.

Each of these gets a small number of searches, but there are thousands of them and they're easier to win. Expect Google to start indexing within 2 to 4 weeks and long-tail rankings to build over **3 to 6 months**. Nobody can guarantee position #1.

---

## 2. Strategy in one sentence
Turn the app's word data into a **free, public Arabic word reference**: one fast, pre-built page per word, verb, pattern and root. Each page answers the search directly and points visitors to the flashcard app with "Add to my flashcards".

---

## 3. The plan

### Phase 1: Fix the basics (I do it; about 1 session)
1. **Branding and metadata:** set a real title, description, Open Graph and Twitter tags, and `theme-color`. Remove the Lovable defaults.
2. **Make your own icons:** self-hosted favicon, Apple touch icon, a 1200×630 social preview image (generated in code) and a web manifest.
3. **Public homepage:** logged-out visitors see a real landing page instead of being redirected to `/auth`. It has a keyword-focused H1 ("Learn Arabic words: plurals, verbs and flashcards"), a feature overview and links into the word reference. Logged-in users go straight to the app, which moves to `/app` so existing behaviour stays the same.
4. **robots.txt:** block `/auth`, `/app` and `/.lovable/`, and add a `Sitemap:` line. Add `noindex` to the login, OAuth consent and 404 pages.
5. **Soft 404s:** single-page-app hosting returns "200 OK" for any URL. The 404 page will get a `noindex` tag, and only real URLs will appear in the sitemap.

### Phase 2: Fix the conjugation engine (I do it; needed before any verb pages)
1. Rewrite the past-tense rules for **sound, hollow, defective, assimilated and doubled** verbs. This includes the sukūn before consonant suffixes, the ـُوا ending, hollow-verb vowel shortening (قُلْتُ / بِعْتُ) and the defective endings (رَمَيْتُ, رَمَوْا).
2. Fix the present tense of defective verbs (يَرْمِي, يَدْعُو) and their jussive and imperative forms (لَمْ يَرْمِ, اِرْمِ).
3. Add **automated tests** that compare every generated form against reference tables, so a page is never published with a form that fails a test.
4. This also fixes the bug in the in-app Verb Drill.

### Phase 3: Public word reference (I do it; this is the main SEO work)
New public pages, all generated from one data source:

| Page type | Example URL | Example title | Approx. count |
|---|---|---|---|
| Word | `/words/kitab-book` | كِتَاب (kitāb): "book" in Arabic, Plural كُتُب | 244 now, then 1,000+ |
| Plural | covered on the word page | "Plural of book in Arabic: كُتُب (kutub)" in an H2 | — |
| Verb conjugation | `/verbs/kataba-to-write` | كَتَبَ (kataba) Conjugation: "to write", All Tenses | 8 now, then 150+ |
| Plural category guide | `/plurals/broken-plurals` | Arabic Broken Plurals: Patterns and 60 Examples | 7 |
| Pattern | `/patterns/fiaal` | The فِعَال Plural Pattern: Examples | ~40 |
| Root family | `/roots/k-t-b` | Root ك-ت-ب: Every Word from k-t-b | Only roots with 3+ words |
| Dialect | `/dialects/levantine-plurals` | Levantine (Shaami) Arabic Plurals | 1 to 2 |
| Indexes | `/words`, `/verbs`, `/roots` | A–Z browse pages | 3 |

**Each word page includes:**
- An H1 with the Arabic word **both with and without harakat** (most people search without diacritics: كتاب, not كِتَاب), plus the transliteration and the English meaning.
- Transliteration variants in the text (kitab / kitaab / ketab) to catch spelling variations.
- Plural form(s), plural type, pattern, grammar note, Fusha vs Shaami, and the root.
- A short Arabic-language line ("جمع كلمة كتاب هو كتب") to catch Arabic-script searches.
- A pronunciation button using the existing SpeakButton.
- A one-question mini quiz ("What's the plural of…?"), which keeps visitors on the page longer.
- Related words: same root, same pattern, same category. This internal linking spreads ranking strength across the site.
- Breadcrumbs plus a **call to action: "Add to my flashcards"**, leading to sign-up and then into the app.

**Structured data (schema.org JSON-LD):** `DefinedTerm`/`DefinedTermSet` for words, `BreadcrumbList` on every page, and `WebSite` + `Organization` on the homepage. I'll skip FAQ markup because Google stopped showing it for sites like this one.

**Content quality:** Google penalizes "thin" auto-generated pages. Each page combines data that's specific to that word (plural, pattern, root family, grammar note, quiz), and I'll only generate root and pattern pages that have enough entries to be useful.

### Phase 4: Make pages fast and indexable (I do it, with Phase 3)
1. **Pre-render at build time:** a Vite plugin runs `react-dom/server` during `vite build` and writes a real HTML file for every public page (`dist/words/kitab-book/index.html`) with the full content and meta tags already in place. React then takes over in the browser. This needs **no server and no change to how Lovable deploys**: it's still just static files.
2. **Auto-generated `sitemap.xml`** built from the same data, so it's always complete.
3. **Code splitting:** public pages won't load Supabase or the flashcard app code, so they stay small and fast. Page speed (Core Web Vitals) is a ranking factor.
4. **Fonts:** preconnect to Google Fonts and load only the needed weights, with `font-display: swap`.
5. **Canonical URLs** on every page, plus `lang="ar"` / `dir="rtl"` on Arabic text spans.
6. **After deploy:** I'll fetch the live pages and confirm Lovable's hosting serves the pre-built HTML. If it doesn't, the fallback is free Cloudflare Pages or Netlify hosting, which takes about 15 minutes of your time.

### Phase 5: Grow the content (I do it, in batches)
1. **Expand the dictionary** from 244 to 1,000+ of the most common nouns and adjectives, and from 8 to 150+ high-frequency verbs. I'll write the entries myself rather than copy them, because dictionary data from Wiktionary and similar sites comes with licence obligations.
2. **Pillar guides** (1,500–2,500 words each, linking to the word pages): "Arabic Plurals: The Complete Guide", "Arabic Verb Forms I–X", "Sun and Moon Letters", "How to Read Harakat", "Levantine vs MSA Plurals".
3. A free **"Arabic Plural Quiz"** page that people can share. A useful free tool is the most realistic way for a small site to earn links from other websites.

### Phase 6: Measure and adjust (shared; ongoing)
- **[YOU]** Once a month, export the Google Search Console "Performance" report (one click to CSV) and paste or upload it here. I'll rewrite titles for pages that appear in results but get few clicks, and add content where Google shows demand.

---

## 4. Your tasks (about 30–45 minutes in total)

| When | Task | Time |
|---|---|---|
| Before I start | Answer the 3 questions in Section 5. | 2 min |
| After Phase 1 deploys | **[YOU]** Add the site to [Google Search Console](https://search.google.com/search-console). Choose "URL prefix" and the "HTML tag" method; I'll add the tag to the code, so you only need to paste me the code Google gives you. Then submit `sitemap.xml`. | 10 min |
| Same day | **[YOU]** Import the site into [Bing Webmaster Tools](https://www.bing.com/webmasters) using "Import from Google Search Console". This also covers DuckDuckGo and Yahoo. | 3 min |
| After Phases 2–3 | **[YOU]** Spot-check about 20 pages I list for Arabic accuracy. | 15 min |
| Optional | **[YOU]** Post the free quiz or guides in places like r/learn_arabic. Links from other sites are the biggest ranking factor I can't build in code. | Optional |
| Monthly | **[YOU]** Export the Search Console CSV (see Phase 6). | 2 min |

---

## 5. Questions before I start

1. **Which repo does Lovable sync with?** This repo (`Arabic-Learning`) only contains a zip file. I can either **(a)** unpack the code here and you connect this repo in Lovable (Project → GitHub), or **(b)** you give this session access to the repo Lovable already syncs with. Option (b) is usually simpler.
2. **What's the live URL?** I need the final domain for canonical URLs and the sitemap. A custom domain such as `benarabic.com` (about $12 a year, connected in Lovable → Settings → Domains) ranks and gets clicked more than a `*.lovable.app` address. It's optional but recommended, and it's best to decide **before** Google indexes the site, because switching domains later costs some ranking.
3. **What's the brand name?** The repo is called "benarabic" and the app header says "بطاقات". I'll use **BenArabic** unless you want something else.

---

## 6. Order of work

1. Phase 1 (basics) and Phase 2 (engine fix): one PR. It's safe to ship right away and it fixes the Verb Drill bug.
2. Phases 3 and 4 (word reference and pre-rendering): one PR. This is the big SEO change.
3. Phase 5 (content batches and guides): several small PRs over time.
4. Phase 6: monthly.

Nothing in the existing flashcard app gets removed. Logged-in users keep the same experience, and the app moves to `/app`.
