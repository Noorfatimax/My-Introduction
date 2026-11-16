# Noor Fatima Portfolio — Design System

Version 1.0 · Source of truth: the landing page artifact (hero → about → selected projects → process → footer)
Purpose: build **case study pages** and the **contact page** so they look and behave like the landing page without re-deciding anything.

---

> **Update (Nov 2026): no gradients anywhere.** Visual panels and placeholders use solid neutrals only: light gray `#EAEAEA`, beige `#FBF0DF`, dark gray `#2F2F2F`. `--pv1/--pv2` and the 135° gradient wash (§1.6, §2.6) are retired.

---

## 0. Principles (the decisions behind everything)

1. **Technical, editorial, human.** Mono labels and bracketed tags (`-- [ hello ]`, `[based in Lahore // open to relocate]`) give an engineering/terminal voice. Large grotesk headlines with tight tracking give an editorial voice. Warmth comes from the illustrated portrait, the pixel companions and the humor.
2. **Each section owns its mood; the system owns the grammar.** Sections change background and palette freely (white hero, grey about, black work, misty process, charcoal footer). Type, spacing, radius, motion and interaction rules stay constant.
3. **Every project gets its own colour world.** A case study is a scoped theme (`--pbg`, `--pink`, …), never a new set of global colours.
4. **Motion is part of the design.** Scroll-driven reveals, glitch text, crosshair and pinned storytelling are signature. They are not decoration to strip on reduced-motion by default (see §9 for the accessibility decision you should revisit).
5. **Show the work through real UI.** Project visuals are small, believable product mocks (white cards on gradients), not stock screenshots.
6. **Human in the loop** is the content theme: copy is plain, first-person, concrete ("I watch real people use it, and fix the moments where they hesitate.").

---

## 1. Foundations

### 1.1 Colour

#### Core (theme-aware; light default, dark via `prefers-color-scheme` or `data-theme="dark"`)

| Token | Light | Dark | Use |
|---|---|---|---|
| `--paper` | `#FFFFFF` | `#101010` | Page background |
| `--ink` | `#111111` | `#F2F2F2` | Primary text, icon chips |
| `--gray` | `#9B9B9B` | `#8A8A8A` | De-emphasised words (e.g. "I'm" in the name) |
| `--ghost` | `#C9C9C9` | `#444444` | Placeholder / ghost text |
| `--line` | `rgba(0,0,0,.12)` | `rgba(255,255,255,.14)` | Hairlines, dividers |
| `--fill` | `rgba(0,0,0,.05)` | `rgba(255,255,255,.06)` | Subtle fills |
| `--accent` | `#0071E3` | same | Focus ring, primary data/UI blue in mocks |

Theme switch rule (copy exactly):
```css
@media (prefers-color-scheme: dark){:root:not([data-theme="light"]){ /* dark tokens */ color-scheme:dark }}
:root[data-theme="dark"]{ /* dark tokens */ color-scheme:dark }
```

#### Dark-section surfaces (fixed in both themes)

| Token | Value | Use |
|---|---|---|
| `--d0` | `#1B1B1B` | Footer background |
| `--d1` | `#2F2F2F` | Showreel panel |
| `--d2` | `#242424` | Raised dark surface |
| `--dink` | `#F2F2F2` | Text on dark |
| `--dmuted` | `#A8A8A8` | Secondary text on dark |
| `--dline` | `rgba(255,255,255,.1)` | Hairlines on dark |
| `--dim` | `#555555` | Unlit state |
| Work section bg | `#0A0A0A` | `.dark` wrapper |
| Unlit heading word | `#3A3A3A` → lit `#F2F2F2` | Scroll-lit headings |

#### Section-specific palettes

- **About statement:** `--sbg #EAEAEA` / `--sink #222222` / `--sdim #D2D2D2` (dark: `#171717` / `#EDEDED` / `#3A3A3A`). Unlit words use `--sdim`, lit words `--sink`.
- **Process ("From build to ship"):** misty grey background `#3A3A3D` + photo, text `#EDEDED` (heading), `#D0D0D0` (body), `#BDBDBD` (small caps), rules `rgba(255,255,255,.4)`.
- **Glass chips (hero domain pills):** `--chipA`, `--chipB`, `--chipEdge`, `--glowA`, `--glowB` (blue→peach gradient, 16px blur, saturate 170%).
- **Crosshair line:** `--xline` `rgba(0,0,0,.18)` light / `rgba(255,255,255,.24)` dark.

#### Project colour worlds (scoped per case study)

Every case study sets seven variables on its root class. Palettes are authored in **OKLCH** so lightness stays consistent across hues.

| Variable | Meaning |
|---|---|
| `--pbg` | Page/section background |
| `--pink` | Primary text |
| `--pmuted` | Body/secondary text |
| `--pline` | Hairlines (≈30% of ink) |
| `--ptag` | Pill background (optional; falls back to `rgba(255,255,255,.45)`) |
| `--pv1`, `--pv2` | Visual panel gradient (135°) |

| Project | Class | `--pbg` | `--pink` | `--pv1` → `--pv2` |
|---|---|---|---|---|
| Healthtech | `.pc-healthtech` | `#0A0A0A` | `#F2F2F2` | `oklch(.82 .11 15)` → `oklch(.88 .10 60)` (rose → peach) |
| Commerce | `.pc-commerce` | `oklch(.92 .09 90)` | `oklch(.28 .06 70)` | `oklch(.88 .13 95)` → `oklch(.75 .15 50)` (yellow → orange) |
| SaaS | `.pc-saas` | `oklch(.91 .07 175)` | `oklch(.27 .05 190)` | `oklch(.85 .12 165)` → `oklch(.78 .10 230)` (mint → sky) |
| Agents | `.pc-agents` | `oklch(.27 .09 285)` | `oklch(.96 .02 285)` | `oklch(.72 .13 295)` → `oklch(.70 .12 250)` (violet → blue) |

**Recipe for a new project palette:** pick one hue `H`. Light project → `bg L≈.91 C≈.08 H`, `ink L≈.27 C≈.05 H(shifted ~15°)`, muted `L≈.40`, line = ink at 30%, visual gradient from `L≈.85 C≈.12 H` to a neighbouring hue ±60° at `L≈.78`. Dark project → `bg L≈.27 C≈.09 H`, ink `L≈.96 C≈.02`, muted `L≈.80 C≈.04`. Keep body-text contrast ≥ 4.5:1.

#### Semantic colours (used inside product mocks only)

| Role | Background | Text |
|---|---|---|
| Success chip `.ok` | `rgba(52,199,89,.18)` | `#1F8A3B` |
| Warning chip `.ok.warn` | `rgba(255,159,10,.2)` | `#B25E00` |
| Info chip `.chipz` | `rgba(0,113,227,.12)` | `#0057B3` |
| Diff add / del | `rgba(52,199,89,.14)` / `rgba(255,59,48,.12)` | `#1B6B2F` / `#A3231B` |
| Recording dot | `#FF3B30` | — |
| Cursor tag / highlight | `#F28C5B` | `#fff` |

#### Companion character colours

Claude `#D97757` (bubble text `#8A3B1D`, dark `#FFDCCB`) · Noor `#A259FF` (bubble text `#3A1B6B`, dark `#EBDDFF`) · Heart `#F0384F`.

---

### 1.2 Typography

**Families** (Google Fonts: Geist 400/500/600, Geist Mono 400/500/600, Cormorant Garamond 500/500i, Caveat 500)

| Token | Stack | Role |
|---|---|---|
| `--f` | Geist, -apple-system, BlinkMacSystemFont, "Helvetica Neue", Arial, sans-serif | Headlines, UI, body in cards |
| `--m` | "Geist Mono", ui-monospace, SFMono-Regular, Menlo, Consolas, monospace | Nav, labels, bios, stats, process, about |
| Cormorant Garamond | — | Loaded, currently unused (reserved for editorial italic accents) |
| Caveat | — | Handwritten note (legacy footer); reserve for rare "annotation" moments |

**Rule of thumb:** *grotesk (Geist) = what I do / headline; mono (Geist Mono) = how it's labelled / annotated.* Sentences that read as personality or explanation (bio, about body, process copy) are mono.

**Type scale** (all fluid via `clamp`)

| Style | Size | Weight | Tracking | Line | Where |
|---|---|---|---|---|---|
| Display / section title | `clamp(44px,9vw,120px)` (mobile `clamp(26px,8vw,34px)`) | 600 | -.04em | 1 | "Selected Projects" `.sechead h2` |
| Process hero | `clamp(30px,5.4vw,80px)` | 600 mono | -.03em | 1.05 | "From build to ship" `.phead` |
| Footer H1 | `clamp(30px,4.6vw,66px)` | 600 | -.035em | 1.08 | `.fh1` |
| Hero name | `clamp(34px,4vw,52px)` | 500 | -.03em | 1.05 | `.hl .name` |
| Statement | `clamp(26px,min(3.8vw,6.2vh),52px)` | 500 | -.03em | 1.13 | `.sline` |
| About heading (mono) | `clamp(28px,min(4vw,6.4vh),58px)` | 600 mono | -.025em | 1.12 | `.shead` |
| Project title H3 | `clamp(30px,4vw,52px)` | 600 | -.035em | 1.05 | `.pcard h3` |
| Process step H2 | `clamp(26px,3.4vw,46px)` | 600 mono | -.02em | 1.1 | `.stext h2` |
| Hero tagline H1 | `clamp(24px,2.6vw,32px)` | 600 | -.03em | 1.12 | `.hr h1` |
| Body large | 17px | 400 | 0 | 1.5 | `.pcard p` |
| Body (page) | 16px | 400 | 0 | 1.5 | `body` |
| About body (mono) | `clamp(14px,1.25vw,17px)` | 400 | 0 | 1.6 | `.sbody` |
| Pull quote | `clamp(16px,1.6vw,20px)` | 600 mono | 0 | — | `.spull` |
| Mono label | 13px | 400 | 0 | — | `.mono` |
| Nav | 14px (12px ≤560px) | 400 mono | 0 | — | `.nav a` |
| Mono small | 12px | 400 mono | 0 | — | stats, panel meta, pills |
| Eyebrow / kicker | 11–12px | 500 | .14–.22em, UPPERCASE | — | `.alabel`, `.kicker`, `.details .dl` |
| Footer links | 14px | 400 | -.01em | 1.4 | `.femail`, `.flist` |

Global: `-webkit-font-smoothing:antialiased`. Headlines always use negative tracking (-.02 to -.04em); the larger the type, the tighter.

---

### 1.3 Spacing & layout

- **Container** `.wrap`: `max-width:1180px; margin-inline:auto; padding-inline:max(16px, env(safe-area-inset-*))`. Nav uses the same wrap with `max-width:none`.
- **Gutter:** 16px minimum on phones.
- **Base unit:** 4px; common steps 6, 8, 10, 12, 14, 16, 20, 24, 28, 36, 44, 56, 70, 80.
- **Fluid section padding:** `clamp(48px,6vw,80px)` (section heads), `clamp(88px,10vw,120px)` top / `clamp(48px,6vw,96px)` bottom (project cards), hero `110px 70px`.
- **Gaps between columns:** `clamp(24px,4vw,64px)` for 2-column copy/visual layouts; 24px for hero grid.
- **Full-viewport scenes:** use `min-height:100vh; min-height:100svh` (always include the `svh` line for mobile browsers).
- **Hero grid:** `300px | minmax(240px,304px) | 300px`, gap 24 (name block · portrait · statement).
- **Project grid:** `1fr 1.15fr` (copy · visual), collapses to 1 column ≤900px.
- **Safe areas:** respect `env(safe-area-inset-*)` on nav, wraps and pinned scenes.

### 1.4 Breakpoints

| Name | Query | Behaviour change |
|---|---|---|
| Tablet / mobile nav | `max-width:900px` | Nav collapses to **Menu** button; hero stacks (name → portrait → text); project grid 1 col; 44px tap areas on icons; hero "dwell" pause |
| Phone | `max-width:700px` | Smaller section headings; About text top-aligned; companions panel pinned bottom; footer 3-col details → 1 col |
| Small phone | `max-width:560px` | Nav font 12px; play button 68px |
| Short screens | `max-height:780px` hides secondary process text; `max-height:640px` disables sticky hero |
| Pointer | `(hover:hover) and (pointer:fine)` gates crosshair/glitch; `(pointer:coarse)` enlarges taps |

### 1.5 Radius

| Token | px | Use |
|---|---|---|
| xs | 4 | Sticky notes, bubble tails |
| sm | 5–6 | Icon chips, focus ring |
| md | 8–10 | Pills (`.pills span` 8), tiles, code blocks |
| lg | 12–14 | Mock inner cards (12), window (14), portrait (14) |
| xl | 16 | Product mocks |
| 2xl | 20 | Project visual panel `.vis` |
| panel | 28 → 12 | Showreel panel (shrinks as it grows) and stacked About top radius 28 |
| pill | 999 | Domain chips, status chips, glass pill, meters |
| circle | 50% | Play button, avatars, dots |

### 1.6 Elevation & materials

| Level | Shadow | Use |
|---|---|---|
| Soft | `0 6px 16px rgba(0,0,0,.08)` | Inner cards, chat bubbles |
| Raised | `0 12px 36px rgba(0,0,0,.18)` | Product mock card |
| Window | `0 30px 60px rgba(0,0,0,.45), 0 0 0 1px rgba(255,255,255,.12)` | Process tool window |
| Sticky | `0 6px 14px rgba(0,0,0,.12)` | Sticky notes |
| Companion avatar | `0 4px 12px rgba(0,0,0,.35)` | Pixel avatars |
| Speech bubble | `0 6px 16px rgba(0,0,0,.08)` | Companion bubbles |

**Materials**
- **Frosted glass** (domain chips, caption bar, mobile menu): `backdrop-filter: blur(14–18px) saturate(1.4–1.7)` + translucent white/near-black + 1px light border.
- **Gradient wash** (project visual): `linear-gradient(135deg, var(--pv1), var(--pv2))`.
- **Mesh glow** (showreel panel): two radial gradients (violet `rgba(120,100,255,.35)` top-left, orange `rgba(255,140,90,.3)` bottom-right) that drift (see motion).
- **Grain** (legacy footer): fractal-noise SVG at 35% multiply.

### 1.7 Iconography

- **Social icons:** 24×24 square chip (radius 5), `background:var(--ink)`, glyph `var(--paper)`, SVG 14×14 `fill:currentColor`. Hit area expands to 44px via `::after{inset:-10px}` on touch/≤900px. Gap 10px (20px on touch).
- **Plus / cross glyph:** a literal `+` in a `<i>` that rotates **135°** on hover in desktop nav, **45°** when the mobile menu is open (turns into ×).
- **Arrow glyph:** `↗` (`&#8599;`) at `.5em`, nudges `translate(2px,-2px)` on hover.
- **Square bullet:** 9–14px solid square before mono eyebrow labels (`.alabel i`, `.stag i`, `.frelo i`).
- **Pixel art:** characters and heart are drawn from string grids into SVG `<rect>`s with `shape-rendering:crispEdges`.

---

## 2. Components

For each: anatomy → variants/states → rules.

### 2.1 Navigation (`.nav`)
- **Anatomy:** fixed bar, 70px high; left: name link (`.nname`, "Noor Fatima"); right: `ul` of links, each prefixed by a `+`. Mono 14px.
- **Behaviour:** `pointer-events:none` on the bar, `auto` on links only (content stays clickable beneath). Colour **switches from `#111` to `#F2F2F2` (`.on-dark`)** based on the background beneath it (set by script). Transition `.25s`.
- **Hover (desktop):** the `+` rotates 135°; characters near the pointer glitch.
- **Mobile (≤900px):** links tuck behind a **+ Menu** button (label becomes **Close**, `+` → ×). Open state: full-width panel `rgba(12,12,12,.97)` with 18px backdrop blur, slides in (`translateY(-8px)→0`, `.25s`), page scroll locked, closes on link click, `Esc`, or resize >900. `aria-expanded` is kept in sync.
- **Items:** About · Work · Process · Contact. For subpages keep the same four anchors pointing to `/#about` etc., plus a visible current-page state (see §7).

### 2.2 Hero block
- **Name block (`.hl`):** mono tagline `-- [ hello ]` over name "I'm **NOOR**" ("I'm" in `--gray`). Right-aligned on desktop, centred on mobile.
- **Portrait (`.portrait`):** ratio 304/464, radius 14, cream placeholder `#FBF0DF`, `object-position:50% 40%`. Desktop: tilts toward the pointer (`perspective(900px)`, ±6°). Asset: `noor-hero.jpg`.
- **Statement (`.hr`):** H1 with a **rotating first word** (Designing / Reimagining / Building / Shaping) then "technology around human needs"; mono bio; social icons.
- **Domain chips (`.domains a`):** glass pills — mono 12px, `8px 16px`, 1px `--chipEdge` border, gradient `--chipA→--chipB`, blur 16 + saturate 170. Hover: `translateY(-2px)`.

### 2.3 Buttons & links
There is no filled marketing button on the landing page; the system uses these:

| Type | Spec |
|---|---|
| **Inline arrow link** | `font 14px`, `border-bottom:1px solid currentColor`, trailing `↗` (`.arr`), hover nudges arrow |
| **Footer link list** | 14px, gap 28, `opacity:.8` on hover, trailing `↗` |
| **Play button (`.play`)** | 92px circle (68px ≤560), white, `#111` glyph, hover `scale(1.1)` `.45s` |
| **Icon chip** | See §1.7; hover `translateY(-3px)` |
| **Dark pill button (legacy `.cpill button`)** | `#14281F` bg, `#EAF2E6` text, 500 14px, `15px 24px`, pill, hover `scale(1.04)` |
| **Menu button** | text-only mono 14px with `+` |

**Contact-page primary button (derived — use this for the form submit):** pill, `background:var(--ink)`, `color:var(--paper)`, Geist 500 14px, `padding:15px 24px`, hover `scale(1.04)`, active `scale(.98)`, focus-visible ring (see §3.2). Secondary: transparent with `1px solid var(--line)`.

### 2.4 Pill / Tag (`.pills span`)
`font:12px Geist; padding:5px 11px; radius 8; color:var(--pink); background:var(--ptag)`. Flex-wrap with 8px gap. Content order: **sector → focus → audience → year** (e.g. Healthtech · Trust & safety · Patient-facing · 2026).

### 2.5 Stat line (`.stat`)
Mono 12px, `--pmuted`. Format: `-31% risky follow-ups · 4.6/5 clarity (example)`. Always up to two metrics separated by ` · `; mark illustrative numbers with `(example)`.

### 2.6 Project card (`.pcard`)
- Full-viewport section (`min-height:100svh`), background `--pbg`, colour `--pink`.
- **Copy column:** H3 → paragraph (max 30em, 17px, `--pmuted`) → stat → pills. Column gap 16px.
- **Visual column (`.vis`):** radius 20, min-height 340, padding `clamp(20px,3vw,44px)`, gradient wash, centred, wraps.
- **Mock (`.mock`):** white 16px-radius card, max-width 290, 14px, padding 16, raised shadow. On card hover it lifts `translateY(-6px) scale(1.02)` (second mock `-3px/1.01`) over `.6s`.
- **Reveal:** `.reveal` fades from `.3` opacity and 40px down to rest in `.9s` when 10% enters the viewport (once).
- **Order of cards:** alternate dark/light/light/dark colour worlds to keep rhythm.

### 2.7 Product-mock kit (reusable inside case studies)
All live on white/light surfaces regardless of theme, using the system blue `#0071E3`.

| Piece | Class | Notes |
|---|---|---|
| Label | `.lbl` | Mono 11px uppercase, `.04em`, `#6E6E73` |
| Row | `.row` | space-between, 8px gap |
| Meter | `.meter > i` | 7px, animates `scaleX 0→1` over 1.4s from left |
| Skeleton line | `.skel` | 8px, `rgba(0,0,0,.08)` |
| Status chip | `.ok`, `.ok.warn` | 11px, 600, pill |
| Caption | `.cap` | 12px `#6E6E73` |
| Step | `.step` + `.dot` / `.dot.off` | 16px dot blue / grey |
| Chat bubbles | `.bubble.them / .ai` | 16px radius, tail corner 4px |
| Mini button group | `.mini span`, `.go` | 12px, 9px radius; `.go` is the primary (blue/white) |
| Product row | `.prod i` | 40px gradient thumb `#FFD6A5→#FFB3BA` |
| Window chrome | `.wbar`, `.wbody` | Traffic-light dots `#FF5F57 #FEBC2E #28C840` |
| Message | `.msg`, `.av` | Avatar 26px `#1B1B1B` |
| Sticky notes | `.st.y/.p/.b` | `#FBE38A / #F7C6C9 / #BFD9FF`, ±1.5° rotation |
| Canvas | `.canvas`, `.frm`, `.link`, `.cur` | Dotted grid 16px, drawn arrows, orange multiplayer cursor |
| Code | `.code` | `#1B1B1B` bg, `#9BE8C0` text, mono 11 |
| Diff | `.diff .d.add/.del` | Mono 12 |
| Tiles/video | `.tiles .tile`, `.rec` | 4-up grid, blinking red dot |
| Dashboard | `.big b`, `.roll`, `.spark` | Big stat 36–56px, -.04em; sparkline stroke 2.5 `#0071E3` draws in 1.6s |

### 2.8 Showreel panel (`.panel`)
Dark `--d1`, radius `28 - 16*p` px, `transform:scale(.88 + .12*p)` where `p` (0→1) is set from scroll position. Mesh glow drifts (14s). Centre play button; mono 12px label top-left, meta bottom. Status message under it (`.reelmsg`).

### 2.9 Section head with lit words (`.sechead`)
Centred display heading split into word spans; words go from `#3A3A3A` to `#F2F2F2` as scroll progresses (`.w.on`, `.2s linear`). Same pattern for the About statement (`--sdim → --sink`, `.25s`).

### 2.10 About statement (`.stmt`)
Mono heading with lit words + mono body (80% ink) + pull quote with 2px left rule + mono note + bracketed location tag `[based in …]` with a square bullet. Slides over the pinned hero (rounded 28px top corners) while the hero dims to 55% brightness and scales to 96%.

### 2.11 Process timeline (`.tl`, `.node`, `.dia`)
- Horizontal track: 1px rule (`rgba(255,255,255,.4)`), tick marks (every 1/5 node width), nodes `clamp(150px,24vw,300px)` wide.
- **Node:** number (`01`…) → role (clamp 12–17px, `#F0F0F0`) → org line (10px uppercase, `.14em`, `#BDBDBD`; hidden ≤700px). A 18px vertical tick drops to the rule.
- **Active indicator:** a 9px white diamond (`rotate(45deg)`) fixed at centre.
- **State:** node opacity = `clamp(1 − |i − f|·.62, .22, 1)`; `aria-selected` marks the active tab. Edge fade via `mask-image` gradient (transparent → black 18% → black 82% → transparent).
- **Click** a node → smooth-scrolls to the matching scroll position.
- **Scroll mapping:** section is `height: 100svh + 450svh`; inner `.about` is `position:sticky;top:0`. Vertical scroll progress `p` drives: horizontal slide, active step `floor(p·n)`, scene tilt.
- **Description line** (`.tdesc`, `aria-live="polite"`) cross-fades (`.25s`).

### 2.12 Process scene / tool window (`.scene`, `.win`)
Left: eyebrow + mono H2 + body. Right: window with traffic-light bar. Entering: `translate3d(56px,0,-140px) rotateY(14deg)` → rest in `.7s`; scene tilts with scroll (`--ry`, `--rx`). Children with `data-a` stagger in using `--i * .2s + .25s`. Six scenes: problem brief (message + stickies), flows & roles (canvas), prototype (chat + preview + code), test (video tiles), build (diff + review + checks), ship (big stat counts up to -31%, rollout bars, sparkline).

### 2.13 Footer (`.foot`, current)
Background `--d0`. Stack: large email link (`.femail`, 14px underlined with ↗; click copies/mailto, `.fcopied` status line, `aria-live`) → link list (LinkedIn, GitHub, Resume) → bracketed location tag → quiet mono credit "Made by Human · 2026 · LHR" (`#6E6E6E`, 14px). Optional `.fh1` display line (`clamp(30px,4.6vw,66px)`, may contain a rotating word).
The earlier sage "sky over sea" footer (`.closing`) is **legacy**; tokens `--sg-*` exist but should not be used on new pages.

### 2.14 Hero companions (Claude & Noor)
Pixel Claude (orange block creature) and a circular Noor avatar run a loop path around the section, bubbles type out a scripted argument (button size, Fitts's Law, etc.), then settle under the portrait with a pixel heart. Bubble: white, 1.5px border in character colour, mono 11–12.5px, max 176px, caret tail, name label 9.5px uppercase bold. Dark theme: `#1B1B1B` bg with light text. Plays on entry (≥35% visible) and resets on exit. Use sparingly: **one scripted moment per page maximum.**

---

## 3. Interaction & motion

### 3.1 Motion tokens

| Token | Value | Use |
|---|---|---|
| `--ease` | `cubic-bezier(.28,.11,.32,1)` | Everything (default for all transitions) |
| Fast | `.25s` | Colour/opacity swaps, nav, menu |
| Standard | `.3–.35s` | Hover transforms, chips, buttons |
| Slow | `.45s` | Icon rotation, play hover |
| Reveal | `.6–.9s` | Mocks, scroll reveals, scene entry (`.7s`) |
| Data | `1.3–1.6s` | Meters, sparklines, count-up |
| Ambient | `6s` sway, `9s` glint, `14s` drift | Looping accents |
| Stagger | `i × .2s + .25s` | Scene child entry |
| Linear | scroll-lit words only | `.2–.25s linear` |

Interpolate tilt/position values from scroll in `requestAnimationFrame`-throttled `scroll` handlers (passive).

### 3.2 States
- **Focus-visible:** `outline:2px solid var(--accent); outline-offset:3px; border-radius:6px`. Never remove.
- **Hover (pointer fine only):** lift (`translateY(-2px…-6px)`), scale (`1.02–1.10`), icon rotate, arrow nudge, underline-colour change. Do not rely on hover for essential information; mobile gets the always-visible variant.
- **Active/selected:** `aria-selected`, diamond marker, opacity ramp.
- **Disabled:** (derived) `opacity:.4; pointer-events:none`.
- **Loading / entry:** hero children `rise` (opacity 0→1, `translateY(24px)→0`, `.9s`, portrait delay `.1s`, copy `.2s`).

### 3.3 Signature interactions
1. **Crosshair cursor** (hero + About only, fine pointer): full-width 1px guide lines + 20px plus at the pointer; native cursor hidden (`body.xh-on *{cursor:none!important}`); fades `.25s`.
2. **Glitch text:** on load, `.tagline` and `.bio` decode from random glyphs over ~1.4s; characters within 28px of the pointer in the nav scramble (55ms ticks, 150ms tail, max 500ms). Character cells are width-locked so layout never shifts. Original text is preserved in a visually hidden `.sr` span for screen readers; glyphs are `aria-hidden`.
   Glyph set: `!<>-_\/[]{}=+*^?#;:&%$`.
3. **Rotating word:** swaps every ~2.6s with the same glitch decode (650ms, per-character settle). Container is `overflow:hidden`, fixed height `1.18em`, `aria-hidden`; full headline duplicated in `aria-label`.
4. **Scroll-lit words** (§2.9).
5. **Stacked reveal:** hero sticks, About slides over it, hero dims/scales.
6. **Pinned process:** vertical scroll → horizontal timeline + 3D scene changes (§2.11–2.12).
7. **Portrait tilt, mock hover lift, panel growth.**

### 3.4 Content reveal rule
`.js .reveal{opacity:.3;transform:translateY(40px)}` → `.in` when 10% visible; unobserve after first reveal. Only apply the `js` hook class from script so content is visible without JS.

---

## 4. Accessibility rules (existing + required)

Already in the build: skip-safe landmarks (`nav aria-label="Main"`, `main`, `footer`), `aria-expanded` on menu, `role="tablist"` / `aria-selected` on process, `aria-live` regions for description and copy confirmation, `aria-hidden` on decorative layers (crosshair, companions, glitch glyphs), `.sr` text for glitched strings, alt text on the portrait, 44px touch areas, `Esc` closes the menu, safe-area insets.

Rules for new pages:
- Every animated headline must expose its final text via `aria-label` or `.sr`.
- Every interactive element must be reachable by keyboard and show the focus ring.
- Body text ≥ 4.5:1; large text (≥24px or 19px bold) ≥ 3:1. Check each project palette.
- Don't convey status by colour alone (chips always include text).

---

## 5. Known gaps & recommendations (found while documenting)

| # | Issue | Recommendation |
|---|---|---|
| 1 | `--gray #9B9B9B` on white is ≈2.8:1 | Fine for the large "I'm" word; do **not** use for body copy. Use `--ink` at 70% (`color-mix`) for secondary text. |
| 2 | `--muted` is referenced (`.sechead p`, `.snote`) but never defined | Define `--muted:#6B6B6B` (light) / `#A0A0A0` (dark). |
| 3 | `reduce=false` is hard-coded, so users with reduced-motion still get all motion | Keep signature motion but at least gate crosshair, glitch, parallax and the companion chase behind `prefers-reduced-motion`, or provide a visible "Reduce motion" toggle. |
| 4 | Duplicated CSS blocks (`.wbar`, `.scene` rules, `.nd` vs `.scene` variants) and legacy footer `.closing` styles | Consolidate into component files when moving to shared CSS; delete `.closing`, `.cpill`, `.cnote`, `.details` unless reused. |
| 5 | Footer "Resume" links to `#contact` | Point to the real PDF before launch. |
| 6 | Footer art (`footer-art.webp`) is a placeholder noted as needing licensed/original art | Not used by the current dark footer; don't reintroduce it. |
| 7 | Mock UI colours (`#0071E3`, `#1D1D1F`, `#6E6E73`) are hard-coded | Alias them as `--ui-blue`, `--ui-ink`, `--ui-muted` in the shared token file. |
| 8 | Cormorant Garamond is loaded but unused | Remove, or adopt for pull-quote italics. |

---

## 6. Token reference (drop-in CSS)

```css
:root{
  /* core */
  --paper:#FFFFFF; --ink:#111111; --gray:#9B9B9B; --ghost:#C9C9C9;
  --line:rgba(0,0,0,.12); --fill:rgba(0,0,0,.05); --accent:#0071E3;
  --muted:#6B6B6B; /* added: was referenced but undefined */
  /* dark surfaces */
  --d0:#1B1B1B; --d1:#2F2F2F; --d2:#242424;
  --dink:#F2F2F2; --dmuted:#A8A8A8; --dline:rgba(255,255,255,.1); --dim:#555555;
  /* type */
  --f:Geist,-apple-system,BlinkMacSystemFont,"Helvetica Neue",Arial,sans-serif;
  --m:"Geist Mono",ui-monospace,SFMono-Regular,Menlo,Consolas,monospace;
  /* motion */
  --ease:cubic-bezier(.28,.11,.32,1);
  /* radius */
  --r-xs:4px; --r-sm:6px; --r-md:10px; --r-lg:14px; --r-xl:16px; --r-2xl:20px; --r-pill:999px;
  /* layout */
  --wrap:1180px; --gutter:16px;
}
@media (prefers-color-scheme: dark){:root:not([data-theme="light"]){
  --paper:#101010; --ink:#F2F2F2; --gray:#8A8A8A; --ghost:#444444;
  --line:rgba(255,255,255,.14); --fill:rgba(255,255,255,.06); --muted:#A0A0A0; color-scheme:dark}}
:root[data-theme="dark"]{
  --paper:#101010; --ink:#F2F2F2; --gray:#8A8A8A; --ghost:#444444;
  --line:rgba(255,255,255,.14); --fill:rgba(255,255,255,.06); --muted:#A0A0A0; color-scheme:dark}

*{box-sizing:border-box}
body{background:var(--paper);color:var(--ink);font-family:var(--f);font-size:16px;line-height:1.5;-webkit-font-smoothing:antialiased}
:focus-visible{outline:2px solid var(--accent);outline-offset:3px;border-radius:6px}
.wrap{max-width:1180px;margin-inline:auto;padding-inline:max(16px,env(safe-area-inset-left,0px)) max(16px,env(safe-area-inset-right,0px))}
.mono{font-family:var(--m);font-size:13px}
```

---

## 7. Applying the system to new pages

### 7.1 Shared page shell (all new pages)
1. Same `<nav>` (Name left, About / Work / Process / Contact with `+`), links as absolute anchors to the home page; mark the current page with `aria-current="page"` and keep the `+` rotated 45° on it.
2. `main` composed of full-width bands; each band is `.wrap` inside a coloured section.
3. Same `<footer class="foot">` (email → links → location tag → credit).
4. Load the same fonts and token block; add the `.js` class from script for reveals.

### 7.2 Case study template

Each case study is **one colour world** (set `--pbg … --pv2` on `<body class="pc-…">`). Section order and what to use:

| # | Band | Content | Components |
|---|---|---|---|
| 1 | **Hero** (100svh, `--pbg`) | Mono eyebrow `[ case study // 2026 ]`, H3-scale title (up to `clamp(40px,6vw,88px)`), one-sentence summary (17px), pills, stat line; visual gradient panel with the hero mock on the right | `.pcard` grid, pills, stat, `.vis`, `.mock` |
| 2 | **At a glance** | 4-up mono grid: Role · Team · Timeline · Platform (labels in uppercase eyebrow style, values 15–16px) | eyebrow, mono text |
| 3 | **Problem** | Scroll-lit statement (the problem in one sentence) + 2-column supporting paragraph | lit words (`--sdim → --sink`), body |
| 4 | **Principles / constraints** | 3 cards separated by `--pline` hairlines | rows + hairlines, no shadows |
| 5 | **Process** | Reuse pinned timeline *only if* the story has ≤6 steps; otherwise a vertical list with `01…` numbering and a mock per step | timeline / scene / window |
| 6 | **Key decisions** | Alternating 2-col rows (copy / `.vis` with mocks). Each decision: H3, 2-3 sentences of "I chose X over Y because Z", mock | `.vis`, `.mock` kit |
| 7 | **Outcome** | Big numbers (`.big b`, 36–56px, -.04em) + sparkline or meter; label each `(example)` until real | dashboard pieces |
| 8 | **Reflection** | Pull quote with 2px left rule (mono 600) + 2 short paragraphs | `.spull`, `.sbody` |
| 9 | **Next project** | Full-width band in the *next* project's colour world with its title as a display heading (`clamp(44px,9vw,120px)`) and arrow link | `.sechead`, arrow link |

Rules:
- One H1 per page (the case study title). Keep step/section headings H2–H3 using the type scale in §1.2.
- Mock UI in case studies must use the mock kit (white card, `#0071E3`, same radii/shadows) so all projects feel authored by one hand.
- Use **at most one** signature interaction beyond reveals per case study (e.g. scroll-lit statement *or* pinned process, not both back to back).
- Image blocks: radius 20, no border; captions in mono 12px `--pmuted`.
- Metrics: mark placeholders `(example)`; replace before publishing.

### 7.3 Contact page template

Tone: calm and direct, inheriting the dark footer rather than inventing a new look.

| Band | Spec |
|---|---|
| **Header** | Background `--d0`, text `--dink`. Eyebrow `-- [ contact ]` (mono 13px, glitch-decode on load), display heading `.fh1`-scale: "Let's build something that keeps a person in the loop." Rotating word optional (e.g. *build / ship / test / shape*). |
| **Primary contact** | Large email link (`.femail`, underline + ↗; click copies and shows `.fcopied` "Copied" confirmation with `aria-live`). |
| **Form** (optional) | Two columns on desktop (1 on ≤900px). Fields: Name, Email, "What are you working on?" (textarea). Style: transparent background, `1px solid var(--dline)`, radius 12, padding 14, mono 14px, label as 11px uppercase eyebrow above, focus = accent ring (§3.2), error = `#FF6B6B` text + message below, never colour alone. Submit = contact-page primary button (§2.3). Success replaces the form with a mono confirmation line and the copied-email fallback. |
| **Elsewhere** | `.flist`: LinkedIn, GitHub, Resume (real PDF). Gap 28, trailing ↗. |
| **Availability** | `[based in Lahore, Pakistan // open to relocate]` with a square bullet. |
| **Credit** | `Made by Human · 2026 · LHR`, `#6E6E73`. |

Contact interactions: crosshair + glitch are allowed on the header only (same `.xhair` zone rule: only add the page's hero to the `zones` list). Keep forms free of motion beyond hover/focus feedback.

### 7.4 Page-level motion budget
- Entry: `rise` (hero) or `.reveal` (sections).
- Maximum one pinned/sticky storytelling section per page.
- Never animate more than two properties per element in a hover.
- Respect `(hover:hover)` and `(pointer:fine)` for pointer-dependent effects.

---

## 8. Voice & content style

- First person, present tense, concrete outcomes ("I frame the problem, prototype with real models in Claude, test with real people…").
- Headlines name the human insight, not the feature ("Showing what the AI doesn't know").
- Humour is dry and short ("Startups call it Tuesday.").
- Bracketed mono tags for meta: `[based in …]`, `-- [ hello ]`.
- Numbers: use `·` between metrics, `×` for multipliers, `-31%` with a hyphen-minus, and label placeholder data `(example)`.
- Years in pills (`2026`) always last.

---

## 9. Component checklist before shipping a page

- [ ] Uses only tokens from §6 (or a scoped project world)
- [ ] Headline sizes come from the §1.2 scale, tracking negative
- [ ] Radii and shadows from §1.5–1.6
- [ ] All transitions use `--ease`
- [ ] Focus ring visible on every control
- [ ] Animated text has an accessible equivalent
- [ ] Layout checked at 1440 / 900 / 700 / 390 and at 640px height
- [ ] `100svh` fallback present wherever `100vh` is used
- [ ] Safe-area insets respected
- [ ] Placeholder stats labelled `(example)` or replaced
- [ ] Contrast checked for the page's colour world

---

## 10. Asset inventory

| File | Used for |
|---|---|
| `noor-hero.jpg` | Hero portrait (illustrated, cream hoodie, brown hijab) |
| `noor-avatar.jpg` | Companion avatar |
| `about-bg.jpg` | Misty process background (legacy `.about` rule) |
| `footer-art.webp` | Legacy footer illustration (placeholder, not in current footer) |
| Google Fonts | Geist, Geist Mono, Cormorant Garamond, Caveat |

---

*Maintenance:* when a new case study or the contact page introduces a new pattern, add it to §2 and the checklist in §9 first, then build. The system should always be a superset of the live site.
