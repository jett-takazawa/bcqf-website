# BCQF Website: Front-End Design Spec

Design spec for the Boston College Quantitative Finance Club one-pager. The content lives in `content_markdown.md`; this file covers how it should look and behave.

The page has two jobs, in this order:

1. **Get students to sign up.** The sign-up button is visible at all times.
2. **Explain who we are.** About Us is the page's one bold moment.

---

## 1. Design direction

- **Look like Boston College, not a fintech startup.** The peer quant club sites we reviewed (Traders@MIT, Cornell Quant Fund, Traders at Berkeley) are white, sans-serif and corporate. BCQF leans into BC's academic serif tradition and its maroon and gold instead, so the site reads as a BC organization at a glance.
- **The logo sets the palette.** Every brand color below was sampled directly from the three BCQF logo files, so the logos sit flush on the page.
- **Spend boldness in one place.** The About Us band (full-bleed maroon, large serif text) is the memorable element. Everything around it stays quiet: no stock photos, no gradients, no drop shadows, no invented stats.
- **Grow without a redesign.** Sponsors, events and photos come later. The layout leaves room for them without placeholder sections today.

---

## 2. Color

### Core palette

| Token | Hex | Source | Use |
|---|---|---|---|
| `--maroon` | `#720F1E` | BCQF white and full logos | Headings, primary button, links, the About band |
| `--gold` | `#BE9050` | BCQF white and full logos | Decorative rules and accents on light backgrounds. Never text on light. |
| `--paper` | `#FEFCFA` | Background of BCQF full logo | Page background |
| `--ink` | `#1F1A17` | — | Body text |
| `--night` | `#070707` | Background of BCQF black logo | Closing sign-up and footer block |

### Supporting colors

| Token | Hex | Source | Use |
|---|---|---|---|
| `--maroon-deep` | `#5A0B17` | Darkened logo maroon | Primary button hover |
| `--gold-light` | `#C99959` | BCQF black logo | Gold text and buttons on maroon or night |
| `--gold-hover` | `#D6AB70` | Lightened gold | Gold button hover |
| `--stone` | `#726158` | BC official Warm Gray 11 | Secondary text on paper (member majors, captions) |
| `--stone-light` | `#B3A49C` | Lightened stone | Secondary text on night |
| `--blush` | `#E8D9D5` | Tinted paper | Secondary text on maroon |
| `--line` | `#E9E1DA` | Tinted paper | Header border after scroll |

### How this relates to BC's official colors

BC's official web values are Maroon `#8A100B`, Gold `#B29D6C`, Black `#000000` and Warm Gray `#726158`. The BCQF logo uses a deeper, cooler maroon (`#720F1E`) and a warmer, more saturated gold (`#BE9050`).

**Use the logo's colors on the site, not BC's.** Two maroons a few shades apart on one page look like a mistake, and the logo files can't change. The site still borrows BC's Warm Gray for secondary text, which ties it to the BC palette.

### Contrast (WCAG 2.1, computed)

| Pair | Ratio | Verdict |
|---|---|---|
| ink on paper | 16.8 : 1 | Body text ✓ |
| maroon on paper | 11.4 : 1 | Any text ✓ |
| paper on maroon | 11.4 : 1 | Any text ✓ |
| stone on paper | 5.8 : 1 | Secondary text ✓ |
| blush on maroon | 8.5 : 1 | Secondary text ✓ |
| gold-light on maroon | 4.5 : 1 | Text ✓ (just passes AA) |
| gold on maroon | 4.1 : 1 | Large text only (24px+) |
| night on gold-light | 7.9 : 1 | Button label ✓ |
| paper on night | 19.7 : 1 | Any text ✓ |
| stone-light on night | 8.4 : 1 | Secondary text ✓ |
| gold on paper | 2.8 : 1 | **Fails.** Decoration only. |
| maroon on night | 1.7 : 1 | **Fails.** Never use. |

Rules that follow from this:

- Gold is never text on a light background, and never a button on a light background.
- Maroon is never text on night. On dark backgrounds, use paper and gold-light.
- Gold text on the maroon band uses `--gold-light`, not `--gold`.

---

## 3. Typography

### Typefaces

| Family | Role | Why |
|---|---|---|
| **Source Serif 4** (Google Fonts) | Headings, body, the About statement | BC's official face is Scala, which BC doesn't license for websites. Source Serif 4 is a free serif in the same scholarly register, and it echoes the bold serif letters of the BCQF wordmark. Its optical sizes sharpen at headline sizes and open up at body sizes. |
| **Open Sans** (Google Fonts) | Buttons, member majors, helper text, nav | BC's official digital typeface. Using it for UI text is a quiet nod to bc.edu. |

```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Open+Sans:wght@400;600&family=Source+Serif+4:opsz,wght@8..60,400;8..60,600&display=swap" rel="stylesheet">
```

### Type scale

| Token | Size | Family / weight | Line height | Used for |
|---|---|---|---|---|
| `--text-sm` | 14px | Open Sans 400/600 | 1.5 | Member majors, helper text, footer |
| `--text-ui` | 16px | Open Sans 600 | 1 | Buttons, nav links |
| `--text-base` | 18px | Source Serif 4 400 | 1.65 | Body text |
| `--text-lead` | 21px | Source Serif 4 400 | 1.5 | Hero tagline, pillar titles (600) |
| `--text-statement` | 24px → 32px | Source Serif 4 400 | 1.4 | The About Us statement |
| `--text-h2` | 28px → 36px | Source Serif 4 600 | 1.2 | Section headings |

Ranges scale fluidly with `clamp()` between 375px and 1280px viewports.

### Type rules

- **Sentence case everywhere:** "About us", "What we do", "Current members", "Sign up". The content file uses title case, so convert it when building.
- **No all-caps labels.** The only uppercase text on the page is the hero name line, because it reproduces the logo lockup (section 4).
- **No single-word accents.** Don't italicize or color one word in a heading.
- **Line length:** body text max 65ch. The About statement max 30em.

---

## 4. Layout

### Grid and spacing

- Container: max width 1120px, centered.
- Side gutters: 24px on mobile, 48px at 768px and up.
- Spacing scale: 4, 8, 12, 16, 24, 32, 48, 64, 96, 128px.
- Section padding (top and bottom): 64px mobile, 96px desktop.
- Alignment: the hero is centered because the logo is symmetric. Every other section is left-aligned.

### Page order (desktop)

```
┌──────────────────────────────────────────────────────────────────┐
│ [BCQF]         About us   What we do   Members   Contact [Sign up]│ sticky header · paper
├──────────────────────────────────────────────────────────────────┤
│                                                                  │
│                         [ BCQF wordmark ]                        │ hero · paper · centered
│            ━━━━━  BOSTON COLLEGE QUANTITATIVE FINANCE CLUB  ━━━━━  │ live-text H1 + gold rules
│                                                                  │
│         Building Boston College's presence in quantitative       │ tagline
│                  trading, research, and development.             │
│                                                                  │
│                    [ Sign up ]     About us                      │ primary CTA + text link
│                                                                  │
├──────────────────────────────────────────────────────────────────┤
│▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓│ about · maroon · full bleed
│▓   About us ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━            ▓│ gold-light heading + rule
│▓                                                                ▓│
│▓   Founded in 2026, the Boston College Quantitative Finance     ▓│ 24–32px serif · paper
│▓   Club is a group of BC students preparing for careers in      ▓│
│▓   quantitative trading, research, and development.             ▓│
│▓                                                                ▓│
│▓   We're here to share what we know ...                        ▓│
│▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓│
├──────────────────────────────────────────────────────────────────┤
│   What we do                                                     │ paper
│                                                                  │
│   ━━━━━━━━━━━━━━━     ━━━━━━━━━━━━━━━     ━━━━━━━━━━━━━━━          │ 2px gold top rules
│   Education           Industry            Community              │
│   Throughout the      connections         We're a small          │
│   year, we run ...    We're building ...  group of ...           │
├──────────────────────────────────────────────────────────────────┤
│   Current members                                                │ paper
│                                                                  │
│   Austin Orsini Chan                Justin Cho                   │ name: serif 600
│   Computer Science                  Physics & Mathematics        │ major: Open Sans, stone
│   ...                               ...                          │
├──────────────────────────────────────────────────────────────────┤
│██████████████████████████████████████████████████████████████████│ close · night
│█  Sign up                                                       █│
│█  Interested in quantitative finance? Fill out our               █│
│█  interest form to get involved.          [ Sign up ]           █│ gold-light button
│█                                          Opens a Google Form   █│
│█                                                                █│
│█  Contact us                                                    █│
│█  bcquantitativefinance@gmail.com                               █│
│█  (415) 960-4567                                                █│
│█                                                                █│
│█  [BCQF black]                     © 2026 Boston College        █│
│█                                   Quantitative Finance Club    █│
│██████████████████████████████████████████████████████████████████│
└──────────────────────────────────────────────────────────────────┘
```

### Section by section

**Header (sticky, paper)**
- Left: `bcqf-white` wordmark, 32px tall, linking to the top of the page.
- Right: anchor links (About us, What we do, Members, Contact) in Open Sans 600 16px ink, then the **Sign up** button.
- After the page scrolls, add a 1px `--line` bottom border. No shadow.
- Mobile (under 768px): hide the anchor links and keep the logo and Sign up button. No hamburger menu; the page is short enough to scroll.

**Hero (paper, centered)**
- The `bcqf-white` wordmark at 360px wide (240px on mobile).
- Below it, the H1 "Boston College Quantitative Finance Club" as **live text**, styled to recreate the full logo's caption: Source Serif 4 600, uppercase, letter-spacing 0.18em, maroon, 14px mobile → 18px desktop, flanked by 2px gold rules.
  - Why live text instead of the `bcqf-full` image: the full logo's caption is only about 24px tall in a 1044px-wide mark. At phone widths it renders around 6–8px and becomes unreadable. Live text stays legible at every size and gives search engines and AI crawlers a real H1.
- Tagline in `--text-lead`, ink, max 36ch.
- Actions: **Sign up** primary button, then an "About us" text link that scrolls to the About band.
- Vertical padding: 96px mobile, 128px desktop. Don't make the hero a full-viewport splash; the maroon band should peek in above the fold on a laptop.

**About us (full-bleed maroon band)**
- This is the bold moment.
- Heading "About us" in Source Serif 4 600 21px, `--gold-light`, followed by a 2px gold rule that runs to the end of the text column. It echoes the rules in the logo.
- Statement in `--text-statement`, paper color, left-aligned, max 30em.
- "Founded in 2026" stays inside the sentence. It is not pulled out as a stat or badge.
- No button here; the sticky header already carries Sign up, and the band reads better uninterrupted.

**What we do (paper)**
- H2 "What we do" in maroon.
- Three pillars in a row at 900px and up, stacked below that. Gap 48px.
- Each pillar: 2px `--gold` top border, 24px padding above the title, title in `--text-lead` 600 maroon, body in `--text-base` ink, max 34ch.
- **No 01 / 02 / 03 numbers.** Education, Industry Connections and Community are parallel pillars, not steps; numbering implies an order that isn't there. Keep the order from the content file.
- No cards, no backgrounds, no icons.

**Current members (paper)**
- H2 "Current members" in maroon.
- Two-column list at 720px and up, one column below.
- Each entry: name in Source Serif 4 600 18px ink; major on the next line in Open Sans 14px `--stone`.
- A name with a LinkedIn link gets a maroon underline. Either add LinkedIn links for every member or remove Jao's, so the list is consistent.
- No photo placeholders. When headshots exist, they go to the left of each name at 64px, square with a 4px radius.

**Sign up + contact + footer (night block)**
- One continuous `--night` block to close the page.
- "Sign up" H2 in paper, the one-line pitch in paper, then a **Sign up** button in gold-light.
- Below the button: "Opens a Google Form" in 14px `--stone-light`, so nobody is surprised by leaving the site.
- "Contact us" H2 in paper. Email and phone as gold-light links, each on its own line.
- Footer row: `bcqf-black` wordmark at 140px wide on the left; © line in 14px `--stone-light` on the right.

---

## 5. Components

### Sign up button

One label everywhere: **Sign up**. No arrow, no "Click here", no "Join now" variant. It links to the Google Form, opens in a new tab, and carries `rel="noopener"`.

| Variant | Where | Background | Text | Hover | Focus |
|---|---|---|---|---|---|
| Primary | Header, hero (on paper) | `--maroon` | `--paper` | `--maroon-deep` | 3px `--maroon` outline, 3px offset |
| Gold | Closing block (on night) | `--gold-light` | `--night` | `--gold-hover` | 3px `--paper` outline, 3px offset |

- Open Sans 600 16px, sentence case.
- Minimum height 48px (header version 40px on desktop, 44px on mobile). Horizontal padding 28px.
- Border radius 3px. No shadow, no scale-on-hover, no gradient.

### Text links

- On paper: maroon, 1px underline, 3px underline offset. Hover thickens the underline to 2px.
- On night: gold-light, same underline treatment.

### Gold rules

The one recurring ornament, taken from the logo. Used in three places only: flanking the hero H1, after the About heading, and on top of each pillar. Always 2px and always `--gold` (or `--gold-light` on dark).

---

## 6. Logo usage

### Files

The three files you shared map like this. Rename them without spaces before putting them in the project, since spaces break URLs.

| You call it | Uploaded file | Rename to | Background baked in |
|---|---|---|---|
| BCQF black | `Elegant_BCQF_Finance_Logo.png` | `public/logos/bcqf-black.png` | `#070707` |
| BCQF white | `Elegant_BCQF_Burgundy_and_Gold_Logo.png` | `public/logos/bcqf-white.png` | `#FEFDFB` |
| BCQF full | `Boston_College_Quantitative_Finance_Logo.png` | `public/logos/bcqf-full.png` | `#FEFCFA` |

### Crop before use

All three files are 1254 × 1254px squares, but the mark only fills the middle strip. Used as-is, the header logo would render tiny inside empty space. Crop to the content plus about 24px of padding:

| File | Content bounds (x1, y1 → x2, y2) | Content size |
|---|---|---|
| bcqf-black, bcqf-white | (114, 444) → (1128, 747) | 1014 × 303px |
| bcqf-full | (105, 444) → (1149, 798) | 1044 × 354px |

### Backgrounds

The files are opaque PNGs, not transparent. Each must sit on a background that matches its baked-in color, or a visible box appears around it.

- `bcqf-white` and `bcqf-full` → only on `--paper`.
- `bcqf-black` → only on `--night`.
- **Never place a logo on the maroon band.** There is no version for it.

Ask whoever made the logo for SVG or transparent PNG exports. With those, the backgrounds stop mattering and a reversed (paper and gold) version for maroon becomes possible.

### Where each logo goes

| Logo | Used for |
|---|---|
| `bcqf-white` | Header, hero |
| `bcqf-black` | Footer |
| `bcqf-full` | Social share image (`og:image`, 1200 × 630 on paper), Instagram, GroupMe, flyers. Anything off-site where the club name must travel with the mark. |
| Q with gold bars | Favicon and social avatar. Crop the Q from `bcqf-white`; it reads at small sizes where the full wordmark won't. |

---

## 7. Motion

One moment only: on page load, the two gold rules flanking the hero H1 draw outward from the text (600ms, ease-out, 200ms delay). It echoes the logo lockup and draws the eye to the club name.

Nothing else animates on its own. No fade-ins on scroll, no hover lifts on cards. Button hovers change color only.

Under `prefers-reduced-motion: reduce`, the rules render at full length with no animation.

---

## 8. Accessibility and quality floor

- All text pairs meet WCAG AA (see the contrast table).
- Visible keyboard focus on every link and button, per the component specs.
- Semantic landmarks: `header`, `main`, one `section` per content block with an `h2`, `footer`.
- One H1 (the hero name line). Logos get alt text "BCQF logo"; decorative rules are CSS, not images.
- Works from 320px wide with no horizontal scroll.
- Tap targets at least 44px on mobile.
- Respects reduced motion.

---

## 9. Content mapping notes

- Convert headings from title case to sentence case.
- Render "What we do" pillars without the "01 —" numbering.
- The content file's "Sign up here →" becomes a button labeled "Sign up".
- Leave room for future sections without building placeholders now. When they exist:
  - **Sponsors:** a row of grayscale logos between "What we do" and "Current members". Every peer club site we reviewed features sponsors prominently.
  - **Events:** a short upcoming list between "About us" and "What we do".
  - **Stats:** only once they're real (seminars held, members, partner firms).

---

## 10. Boston College brand compliance

BC restricts its official marks, and this affects what can go on the site:

- BC's brand guide says the wordmark and other elements of its graphic identity are for official Boston College use only.
- BC's indicia policy says student organizations need approval from the Campus Licensing Coordinator (who consults the communications office) to use BC indicia.

In practice: **don't use the BC seal, the BC wordmark, or the Baldwin eagle** on the site. The club's own BCQF logo and the maroon and gold palette are a separate matter, but because the club name includes "Boston College", it's worth a short email to the Office of University Communications (ouc-branding@bc.edu) or Student Involvement to confirm before launch. This isn't legal advice, just the policy as published.

---

## 11. Build notes

Starter tokens for the stylesheet:

```css
:root {
  /* core palette (sampled from BCQF logos) */
  --maroon: #720F1E;
  --gold: #BE9050;
  --paper: #FEFCFA;
  --ink: #1F1A17;
  --night: #070707;

  /* supporting */
  --maroon-deep: #5A0B17;
  --gold-light: #C99959;
  --gold-hover: #D6AB70;
  --stone: #726158;        /* BC Warm Gray 11 */
  --stone-light: #B3A49C;
  --blush: #E8D9D5;
  --line: #E9E1DA;

  /* type */
  --font-serif: "Source Serif 4", Georgia, "Times New Roman", serif;
  --font-sans: "Open Sans", system-ui, -apple-system, "Segoe UI", sans-serif;

  --text-sm: 0.875rem;
  --text-ui: 1rem;
  --text-base: 1.125rem;
  --text-lead: 1.3125rem;
  --text-statement: clamp(1.5rem, 1.293rem + 0.884vw, 2rem);   /* 24px at 375 → 32px at 1280 */
  --text-h2: clamp(1.75rem, 1.543rem + 0.884vw, 2.25rem);       /* 28px at 375 → 36px at 1280 */

  /* layout */
  --container: 1120px;
  --gutter: 24px;
  --section-pad: 64px;
  --radius: 3px;
}

@media (min-width: 768px) {
  :root { --gutter: 48px; --section-pad: 96px; }
}

body {
  background: var(--paper);
  color: var(--ink);
  font-family: var(--font-serif);
  font-size: var(--text-base);
  line-height: 1.65;
}
```

Other build details:

- Output static HTML (Astro or plain HTML). No client-side-only rendering, so search engines and AI crawlers see the full content.
- `<title>`: Boston College Quantitative Finance Club (BCQF)
- Meta description: the hero tagline.
- `og:image`: `bcqf-full` composed on a 1200 × 630 paper canvas.
- Add `Organization` JSON-LD with the name, email, logo and sign-up URL.

---

## Research notes

**Boston College brand**
- Official primary colors: Maroon (PMS 202, `#8A100B`), Gold (PMS 874 metallic, `#B29D6C`), Black, and Warm Gray 11 (`#726158`). BC's guidance is that maroon is always used at full strength, never as a tint.
- Typefaces: Scala and Scala Sans for print. Scala isn't licensed for BC websites; BC's digital identity uses Open Sans.
- bc.edu itself uses a sticky header with utility links (Apply, Visit, Give), a full-width campus hero, modular card sections and a dense footer. Too institutional to copy for a one-pager, but the serif-plus-sans mix and maroon accents carry over.

**Peer quant club sites**
- **Traders@MIT:** nav with a persistent Join link; hero headline with two buttons; a three-stat strip; four numbered pillars (Competition, Education, Industry Connections, Community); exec board headshot grid; sponsor logo carousel; contact; footer.
- **Cornell Quant Fund:** nav with Apply; logo-and-tagline hero; "Who we are"; three mission pillars (Education, Outreach, Engineering); 14 sponsor logos; "Join our team" call to action; footer.
- **Traders at Berkeley:** campus-photo hero with two buttons (Upcoming Events, Join Us); About with impact metrics; calendar; programs; placement outcomes; mailing-list signup repeated mid-page and in the footer.

**Patterns we're keeping:** Join/Sign up in the nav, in the hero and again at the bottom. Three or four pillars. A roster.
**Patterns we're skipping for now:** stats strips, sponsor carousels and placement walls, until BCQF has real numbers and partners.

### Sources

- [Boston College Primary Brand Colors](https://bc.edu/content/bc-web/offices/office-of-university-communications/policies-guidelines/print-colors.html)
- [Boston College Brand Guide (PDF)](https://ccc.bc.edu/content/dam/bc1/offices/ouc/branding/brand-guide.pdf)
- [Boston College Visual Identity](https://styles.bc.edu/visual-identity)
- [Boston College Fonts and Typography](https://bc.edu/content/bc-web/offices/office-of-university-communications/policies-guidelines/typography-print.html)
- [Boston College Use of Indicia Policy (PDF)](https://ccc.prod.bc.edu/content/dam/bc1/sites/policies/UseofBCIndiciapostedpolicy%204-22-02%20(00025778).DOCX%20%20-%20%20Compatibility%20Mode.pdf)
- [Boston College homepage](https://www.bc.edu/)
- [Traders@MIT](https://traders.mit.edu/) and [About page](https://traders.mit.edu/about)
- [Cornell Quant Fund](https://www.cornellquantfund.org/)
- [Traders at Berkeley](https://traders.studentorg.berkeley.edu/)
