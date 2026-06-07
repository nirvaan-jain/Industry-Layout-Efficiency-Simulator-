# Industrial Layout Efficiency Simulator
Live link: extraordinary-gaufre-a8d38a.netlify.app
<img width="1439" height="778" alt="Screenshot 2026-06-07 at 4 21 49 PM" src="https://github.com/user-attachments/assets/b8e04e88-cddb-463a-992b-a66b7ba9cf00" />

> AI-powered factory layout planner. Upload a floor photo, describe your operation, and Gemini generates three optimised layouts — cost-effective, balanced, output-max — with metrics, reasoning, and floor plans.
<img width="1439" height="384" alt="Screenshot 2026-06-07 at 4 22 08 PM" src="https://github.com/user-attachments/assets/3ddbf45c-33a9-4e6e-a423-689b290f53f3" />

---

## What it does
<img width="1440" height="788" alt="Screenshot 2026-06-07 at 4 23 04 PM" src="https://github.com/user-attachments/assets/9217bd55-eb8d-454c-a6be-45229c3a3328" />

You upload a photograph of your factory floor — empty site or existing operation — and describe what you make. The simulator analyses your current layout, diagnoses bottlenecks, then generates three complete redesign proposals with engineering-quality floor plans, itemised change lists, and a PDF export ready to hand to a contractor.
<img width="2604" height="1498" alt="image" src="https://github.com/user-attachments/assets/d7571d83-632a-4860-a2f4-39708c93020a" />
<img width="1678" height="1502" alt="image" src="https://github.com/user-attachments/assets/43ef3119-10e5-4a59-b526-765a9eb1ecb7" />

The entire tool runs in a single HTML file. No backend. No build step. No account required.

---

## Features

### Five-step workflow

| Step | What happens |
|------|-------------|
| **01 Context** | Describe your facility — industry, product, dimensions, scale, workforce, shift pattern, safety standard, material flow preference |
| **02 Upload** | Drop a photo of the floor. Gemini Vision auto-detects visible machinery and populates the tag list |
| **03 Analysis** | Efficiency score (0–100), four sub-scores, a bottleneck heatmap, and a severity-ranked issue list |
| **04 Proposals** | Three layout options — Cost-Effective, Balanced, Output-Optimised — each with a deterministic SVG floor plan |
| **05 Compare** | Side-by-side table, radar chart across six dimensions, AI recommendation, and export |

### Layout proposals
<img width="2094" height="1530" alt="image" src="https://github.com/user-attachments/assets/27dfa0de-480e-4be6-839b-801021263ae1" />

Each of the three proposals includes:
- Top-down SVG floor plan generated from machine grid positions
- Efficiency score, implementation cost, timeline
- **Full itemised change list** — every change with FROM / TO / individual cost / operational impact
- Key advantages and trade-offs
- Implementation roadmap by week and phase

### Selection and export
<img width="2362" height="1532" alt="image" src="https://github.com/user-attachments/assets/5daf6176-57e7-44a4-a336-4423a87fad3e" />

- Click **Select this path** on any proposal to open the full detail view
- Floor plan + complete change breakdown in one screen
- **Download PDF report** — multi-page A4 document with oxblood cover page, floor plan, and the full change table with per-item costs and a total

### Analysis quality
- **Gaussian zone heatmap** — hot spots modelled as bell curves (intake congestion, central flow junction, output staging, QC overload) rather than random noise
- Tricolour gradient: low intensity (warm tint) → moderate (orange) → critical (oxblood)
- Hover any cell for zone name, row, intensity percentage, and criticality label

---

## Tech stack

| Layer | What |
|-------|------|
| AI — vision + text | `gemini-2.5-flash` via Generative Language API |
| AI — image generation | `gemini-2.5-flash-image` (optional, free tier ~500/day) |
| Frontend | Vanilla HTML / CSS / JS — no framework |
| Charts | Chart.js (CDN) |
| Fonts | Fraunces, Inter, JetBrains Mono (Google Fonts) |
| PDF | `window.print()` with print stylesheet — no library |
| Storage | `localStorage` for session persistence |

**No backend. No build step. No dependencies to install.**

---

## Getting started

### 1. Get a Gemini API key

Go to [aistudio.google.com/apikey](https://aistudio.google.com/apikey) and create a free key. The free tier includes:
- Gemini 2.5 Flash — generous daily quota for text and vision
- Gemini Flash Image — ~500 image generations per day

### 2. Paste the key into the file

Open `simulator.html` in a text editor. Near the top of the `<script>` block, find:

```js
// ⚠️ Personal/demo use only — this key is visible in the file source
const GEMINI_API_KEY = "PASTE_KEY_HERE";
```

Replace `PASTE_KEY_HERE` with your key. Save the file.

### 3. Open in a browser

```bash
open simulator.html
# or just double-click it
```

That's it. No `npm install`. No server.

> **Note on the API key:** Because this is a single-file client-side app, the key is visible in the HTML source. Use it for personal or demo purposes only. Do not commit the file with a live key to a public repository.

---

## File structure

```
/
├── simulator.html     # The entire application — HTML, CSS, JS in one file
├── landing.html       # Marketing landing page linking to the simulator
└── README.md
```

### Inside `simulator.html`

The script block is organised into clearly commented sections:

```
CONFIG          API key, model IDs, endpoints
STATE           Single global state object
UTILITIES       $(), elCreate(), toast(), showLoading()
GEMINI CLIENTS  geminiGenerate(), geminiImage()
PROMPTS         System prompts for detection, analysis, layout generation
STEP NAV        showStep(), scrollToSection(), validateAndScroll()
FORM WIRING     Chip groups, selects, file upload, machine tags
ANALYSIS        runAnalysis(), renderAnalysis(), heatmap renderer
PROPOSALS       generateProposals(), displayProposals(), selectLayout()
FLOOR PLANS     generateEngineeringDrawing() — deterministic SVG renderer
SIDE PANEL      showProposalDetail() — change-list breakdown
SELECTED VIEW   renderSelectedDetail() — full detail after selection
PDF EXPORT      generatePDF() — opens print-ready document in new tab
COMPARE         displayComparison(), radar chart (Chart.js)
STORAGE         saveState(), loadState(), localStorage persistence
INIT            DOMContentLoaded wiring
```

---

## Design

The visual language is based on the [Fauna agency site](https://www.awwwards.com/sites/fauna) — editorial, confident, oxblood and orange.

| Token | Value |
|-------|-------|
| Oxblood | `#590000` |
| Orange | `#FF4000` |
| Cream | `#F5F1EA` |
| Near-black | `#0A0A0A` |
| Carbon | `#1A1A1A` |
| Gray | `#8C8C8C` |
| Display font | Fraunces (variable, opsz + SOFT axis) |
| Body font | Inter |
| Mono font | JetBrains Mono |

The layout uses a **sliding step track** — all five sections sit side-by-side in a fixed viewport and translate horizontally between steps. No page reloads, no scroll-jacking.

---

## API call structure

### Text / vision (analysis + layout generation)

```js
POST https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash:generateContent?key=KEY

{
  contents: [{ role: "user", parts: [
    { inline_data: { mime_type: "image/jpeg", data: "<base64>" } },
    { text: "Your prompt..." }
  ]}],
  systemInstruction: { parts: [{ text: "System prompt..." }] },
  generationConfig: {
    responseMimeType: "application/json",
    responseSchema: { /* JSON schema */ },
    temperature: 0.7
  }
}
```

### Image generation

```js
POST https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-image:generateContent?key=KEY

{
  contents: [{ parts: [{ text: "Floor plan prompt..." }] }],
  generationConfig: { responseModalities: ["IMAGE"] }
}
```

---

## Known limitations

- **API key in plaintext** — by design for a single-file demo. Rotate the key if you share the file.
- **Gemini image generation reliability** — the free tier is throttled and the model occasionally produces non-technical output. The SVG floor plan renders immediately as a placeholder and stays if image generation fails.
- **Scores are AI estimates** — efficiency scores and cost projections are Gemini's reasoning, not a simulation. They are plausible, not engineering-grade. Label them as estimates when presenting to stakeholders.
- **Session persistence** — state is saved to `localStorage`. Clearing browser storage resets the session.
- **Single file / context limits** — very long conversations with large images may approach token limits. Refresh and re-run if you hit an error on large inputs.

---

## Roadmap ideas

- [ ] Replace Gemini image generation with a fully deterministic SVG renderer using the machine grid positions (better quality, no quota dependency)
- [ ] Drag-and-drop floor plan editor — move machine blocks, auto-redraw flow arrows
- [ ] Real Gemini API integration replacing the current mock analysis data
- [ ] Multi-facility comparison across sessions
- [ ] Contractor export package — PDF + JSON + DXF stub

---

## Screenshots

| Step | Description |
|------|-------------|
| Hero | Oxblood full-bleed with large Fraunces editorial type |
| Context | 13-field brief form with chip selectors and underline inputs |
| Analysis | Large efficiency number, sub-score bars, Gaussian heatmap |
| Proposals | Three cards with SVG floor plans and Select buttons |
| Selected | Full FROM/TO change list with individual costs |
| PDF | Multi-page print document with cover page and change table |

---

## License

MIT — use freely, modify freely, don't hold the author liable.

---

## Acknowledgements

- **Google Gemini** — vision, text, and image generation
- **Fauna / BONHOMME** — design language reference ([thefauna.co.uk](https://www.thefauna.co.uk))
- **Chart.js** — radar chart
- **Fraunces** — display typeface by Undercase Type
- **J.v.G. Thoma GmbH** — engineering drawing reference for floor plan style
