# design-tokens.md — EquityEarn
# Source of truth for all colour, typography, radius, shadow, and spacing values.
# Read this before building any screen or component.

---

## Figma sources

- Main design file: qYyvSz3WKL0StRPcIdFPTP
- shadcn library: v8toW8lsKvo6KXkPTzJOBD (Jan 2026 community)
- Equity Earn Library: ubekstH5XQHdhPaSd45zps

---

## Colour tokens

### oklch equivalents (shadcn v4 format — use these in globals.css)

| Token              | Hex      | oklch value              |
|--------------------|----------|--------------------------|
| primary (teal)     | #14b8a6  | oklch(0.704 0.14 192)    |
| primary-foreground | #ffffff  | oklch(1 0 0)             |
| muted              | #f4f4f5  | oklch(0.97 0 0)          |
| muted-foreground   | #71717b  | oklch(0.556 0 0)         |
| border             | #e4e4e7  | oklch(0.922 0 0)         |
| ring               | #14b8a6  | oklch(0.704 0.14 192)    |
| health-safe        | #22c55e  | oklch(0.723 0.191 149)   |
| health-warning     | #eab308  | oklch(0.853 0.17 84)     |
| health-alert       | #f97316  | oklch(0.747 0.198 47)    |
| health-critical    | #ef4444  | oklch(0.637 0.237 25)    |

### globals.css font wiring (shadcn v4)

In @theme inline block, change:
  --font-sans: var(--font-geist-sans);   ← default
to:
  --font-sans: var(--font-figtree);      ← EquityEarn
  --font-heading: var(--font-playfair);  ← logo wordmark only

### CSS variables (app/globals.css :root)

```css
:root {
  --background:            0 0% 100%;
  --foreground:            240 10% 3.9%;
  --card:                  0 0% 100%;
  --card-foreground:       240 10% 3.9%;
  --primary:               174 72% 40%;      /* #14b8a6 — teal */
  --primary-foreground:    0 0% 100%;
  --secondary:             240 4.8% 95.9%;
  --secondary-foreground:  240 5.9% 10%;
  --muted:                 240 4.8% 95.9%;   /* #f4f4f5 */
  --muted-foreground:      240 3.8% 46.1%;   /* #71717b */
  --border:                240 5.9% 90%;     /* #e4e4e7 */
  --input:                 240 5.9% 90%;
  --ring:                  174 72% 40%;      /* matches primary */
  --radius:                0.375rem;         /* 6px — base radius for inputs */

  /* EquityEarn-specific tokens */
  --approve:               174 72% 40%;      /* #14b8a6 */
  --health-safe:           142 71% 45%;      /* #22c55e */
  --health-warning:        48 96% 53%;       /* #eab308 */
  --health-alert:          25 95% 53%;       /* #f97316 */
  --health-critical:       0 84% 60%;        /* #ef4444 */
  --status-pending:        25 95% 53%;       /* #f97316 */
  --status-approved:       174 72% 40%;      /* #14b8a6 */
  --status-rejected:       0 84% 60%;        /* #ef4444 */
}
```

### Hex reference (for inline Tailwind classes)

```
primary (teal):         #14b8a6
primary-hover:          #0d9488
primary-active:         #0f766e
foreground:             #09090b
muted-foreground:       #71717b
border:                 #e4e4e7
muted bg:               #f4f4f5
white:                  #ffffff
dark nav bg:            #1a1b1d
health-safe:            #22c55e
health-warning:         #eab308
health-alert:           #f97316
health-critical:        #ef4444
text-secondary:         #737373  (Equity Earn Library local variable)
stroke:                 #dddddd  (Equity Earn Library local variable)
```

### Confirmed variable IDs (EquityEarn file — use for Figma MCP binding)

```
muted-foreground (shadcn):    VariableID:022a84dc6b29f9af780898b12aa2c4bb4aa781d2/3003:5
Text/Secondary (EE Library):  VariableID:40002047:4301
Stroke (EE Library):          VariableID:40002051:3308
```

### Colour binding rules

Never hardcode hex fills or strokes. Always bind to a variable:

```js
// Get variable
const mutedFgVar = await figma.variables.getVariableByIdAsync(
  "VariableID:022a84dc6b29f9af780898b12aa2c4bb4aa781d2/3003:5"
);
// Bind fill
node.fills = node.fills.map(p =>
  p.type === "SOLID"
    ? figma.variables.setBoundVariableForPaint(p, "color", mutedFgVar)
    : p
);
// Bind stroke
node.strokes = node.strokes.map(p =>
  p.type === "SOLID"
    ? figma.variables.setBoundVariableForPaint(p, "color", strokeVar)
    : p
);
```

### Variable → use case map

| Variable             | Value    | Use on                                              |
|----------------------|----------|-----------------------------------------------------|
| muted-foreground     | #71717b  | Icon fills/strokes, placeholder text, captions      |
| Text/Secondary       | #737373  | Nav tab labels, button text on dark bg, avatar text |
| Stroke               | #dddddd  | Avatar/outline button borders on dark bg            |
| foreground           | #09090b  | Primary body text, headings, filled input values    |
| border               | #e4e4e7  | Input borders, card borders, divider lines          |
| primary              | #14b8a6  | CTA buttons, focus rings, active states             |

---

## Typography

### Figma text style source

All Figma text nodes MUST use published shadcn text styles.
Apply via: `figma.importStyleByKeyAsync(key)` → `node.textStyleId = style.id`
Never set raw font family/size/weight on a Figma text node.
Exception: Playfair Display logo wordmark — no shadcn style exists, set manually.

### Title rule — CRITICAL

All screen titles, card titles, and modal titles use **Text-2xl/Bold**.
Never use Text-2xl/Semi Bold for titles — that is for subheadings only.
In code: `className="text-2xl font-bold leading-8 text-foreground"`

### Style mapping

| Role             | shadcn style name  | Size | Weight | Line-height |
|------------------|--------------------|------|--------|-------------|
| Screen/card title | Text-2xl/Bold     | 24px | 700    | 32px        |
| Subheading       | Text-sm/Regular    | 14px | 400    | 20px        |
| Field label      | Text-sm/Medium     | 14px | 500    | 20px        |
| Input text       | Text-base/Regular  | 16px | 400    | 24px        |
| Button text      | Text-sm/Medium     | 14px | 500    | 20px        |
| Small/caption    | Text-xs/Regular    | 12px | 400    | 16px        |
| Nav/tab links    | Text-sm/Semi Bold  | 14px | 600    | 20px        |
| Logo wordmark    | (raw Playfair Bold — no shadcn style) | — | 700 | normal |
| Hero headline    | Text-3xl/Bold      | 30px | 700    | 36px        |

### Style keys (shadcn library v8toW8lsKvo6KXkPTzJOBD)

```
Text-2xl/Bold        (confirm key after publishing)
Text-2xl/Semi Bold   a403861547cc1c8464026da164abfd12ba4fd43b
Text-sm/Semi Bold    6a7860fc660c6a54ea13a533688adb3a6c76aeff
Text-sm/Medium       1ce3cf0aa97d7b16a3f58c9df234853e4dfd8619
Text-sm/Regular      9aa1d9f12112ba31b839e59b332bf325909c2264
Text-base/Regular    6371dbe82a92cbbfddda5f0ce03d7c04e7a89131
Text-xs/Regular      3e787b0754b9c073abed15f4b6bd30999c3a261e
Text-3xl/Bold        8d2e70f58279e0d52bfd060c144479264c849717
```

### Code typography (React)

```
Role           Component        Family            Weight  Size  Line-height
─────────────  ───────────────  ────────────────  ──────  ────  ───────────
Screen title   h1 / CardTitle   Figtree           700     24px  32px
Subheading     CardDescription  Figtree           400     14px  20px
Field label    Label            Figtree           500     14px  20px
Input text     Input            Figtree           400     16px  24px
Button text    Button           Figtree           500     14px  20px
Small          span             Figtree           400     12px  16px
Logo wordmark  span             Playfair Display  700     18px  normal
Hero headline  p                Figtree           700     32px  1.2
```

Font setup (app/layout.tsx):

```tsx
import { Figtree, Playfair_Display } from "next/font/google"

const figtree = Figtree({
  subsets: ["latin"],
  weight: ["400", "500", "700"],
  variable: "--font-figtree",
})
const playfair = Playfair_Display({
  subsets: ["latin"],
  weight: ["700"],
  variable: "--font-playfair",
})
```

---

## Radius rules — CRITICAL

Three different values. Never mix them up.

| Element                | Value           | Tailwind class   |
|------------------------|-----------------|------------------|
| Outer card containers  | 14px            | rounded-[14px]   |
| Input fields           | 6px             | rounded-md       |
| All buttons            | 9999px (pill)   | rounded-full     |
| Badges / status chips  | 9999px (pill)   | rounded-full     |
| Modals                 | 24px            | rounded-[24px]   |
| Nav bar card           | 16px            | rounded-2xl      |

Never use rounded-[14px] on inputs.
Never use rounded-md on buttons.

In Figma MCP — when resizing a component instance that has absolutely-positioned
children (e.g. the logo), use rescale() not resize(). resize() breaks internal
proportions of nested absolute elements.

---

## Shadow rules

| Element                     | Shadow value                                  |
|-----------------------------|-----------------------------------------------|
| Google / outline button     | shadow-[0px_1px_1px_rgba(0,0,0,0.10)]        |
| Onboard / main content card | drop-shadow 0px 1px 1.5px rgba(0,0,0,0.10)  |
| All other elements          | none                                          |
| Modal backdrop              | bg-black/50 backdrop-blur-sm                 |

---

## Brand assets

| Asset                        | Key                                        | Library              | Node    |
|------------------------------|--------------------------------------------|----------------------|---------|
| Logo/Full/Horizontal/Black   | 542dd8e54c0fd4fb3f202a024a0254289c8294a8   | 📚 Equity Earn Library | 10:16  |

Always import this component — never rebuild logo from shapes + text.
Use rescale() not resize() when sizing the instance.

### Exported assets (public/images/)

```
logo-symbol.svg     Figma layer: Logo/Symbol/Black
logo-e.svg          Figma layer: E
google-icon.svg     Figma layer: Google icon
login-bg.jpg        Figma layer: bence-balla-schottner-...
```

Never use Figma MCP temp URLs in production — always export to /public/images/.

---

## Health score logic

```tsx
// Formula: ((35 - LTV) / 35) * 100
// LTV formula: (currentLoan / totalPledgedValue) * 100

function getHealthColour(score: number) {
  if (score >= 50) return "bg-[#22c55e]"   // safe
  if (score >= 30) return "bg-[#eab308]"   // warning
  if (score >= 15) return "bg-[#f97316]"   // alert
  return "bg-[#ef4444]"                    // critical
}
```

### LTV alert thresholds

| Threshold | State                      |
|-----------|----------------------------|
| 25.00%    | Yellow warning banner      |
| 28.00%    | Orange warning banner      |
| 30.00%    | Red warning (loan cap)     |
| 35.00%    | Liquidation trigger        |
