# component-map.md — EquityEarn
# Component keys, usage rules, and Figma MCP patterns.
# Read this before building any screen or component.

---

## Install (shadcn/ui)

```bash
npx shadcn@latest init
npx shadcn@latest add card input label button separator
npx shadcn@latest add badge table tabs dialog slider progress
npx shadcn@latest add select dropdown-menu switch toast sheet
npm install lucide-react
```

---

## Navigation — canonical nav bar (post-login)

Every post-login screen MUST use this component as its first child at y:0.
Never use a plain FRAME for the nav bar.
Pre-login screens (Sign In, Sign Up) use the inline logo with no nav bar.

| Property      | Value                                      |
|---------------|--------------------------------------------|
| Name          | Navigation/Nav bar/Desktop                 |
| Library       | 📚 Equity Earn Library                     |
| Component key | 332c90e7fd5e5d94929dc1627ee50a08eec39b61  |
| Node          | 16:85 in file ubekstH5XQHdhPaSd45zps       |
| Size          | 1440 × 72px                               |

```js
// Figma MCP usage
const navBarMaster = await figma.importComponentByKeyAsync(
  "332c90e7fd5e5d94929dc1627ee50a08eec39b61"
);
const navInst = navBarMaster.createInstance();
navInst.resize(1440, 72);
navInst.x = 0;
navInst.y = 0;
frame.insertChild(0, navInst); // always index 0
```

```tsx
// React usage
import { NavBar } from "@/components/equityearn/nav-bar"
<NavBar activePage="dashboard" />
```

### Screens confirmed using this component

| Screen           | Frame node    | Status     |
|------------------|---------------|------------|
| KYC Handoff      | 40002688-331  | ✅ Updated |
| Company Selection| 40002862-790  | ✅ Built   |

---

## Logo

Never rebuild. Always import the master component.

| Property      | Value                                      |
|---------------|--------------------------------------------|
| Name          | Logo/Full/Horizontal/Black                 |
| Library       | 📚 Equity Earn Library                     |
| Component key | 542dd8e54c0fd4fb3f202a024a0254289c8294a8  |
| Node          | 10:16 in file ubekstH5XQHdhPaSd45zps       |
| Native size   | ~296 × 77px                               |

```js
// Import and scale to 40px height
const logoMaster = await figma.importComponentByKeyAsync(
  "542dd8e54c0fd4fb3f202a024a0254289c8294a8"
);
const logoInst = logoMaster.createInstance();
logoInst.rescale(40 / logoInst.height); // rescale() not resize()
```

---
## Full-bleed page layouts (Sign In, Sign Up)

Pre-login screens fill the full viewport — no padding, no max-width,
no border around the outer wrapper.

✅ Correct:
  <div className="min-h-screen flex">
    <div className="flex w-full overflow-hidden">

❌ Wrong — creates a visible border/gap:
  <div className="min-h-screen flex items-center justify-center bg-muted p-4">
    <div className="flex w-full max-w-[1333px] h-[930px] rounded-[14px] border">

Post-login screens (dashboard, KYC, etc.) use the standard layout:
  bg-muted full page → centered card with rounded corners and border


## With Label — canonical input pattern

Every form field uses this component. Never use bare Input without Label.

| Item                  | Key                                        |
|-----------------------|--------------------------------------------|
| Component SET         | 31498c87ad140feb48bc04bf2610be7ee1e81e03  |
| State=default variant | 4eecd4bcff4883472de19d4ff45cd997d82b912e  |
| State=active variant  | 1cd08e397f6e700653bff7905f50e652a344f194  |

### Component properties (setProperties())

| Property key      | Type    | Default       | Controls                                |
|-------------------|---------|---------------|-----------------------------------------|
| Label#3098:6      | TEXT    | "Email"       | Label text above the input              |
| Input#3097:0      | TEXT    | "Placeholder" | Placeholder / filled value text         |
| Show Text#3052:0  | BOOLEAN | false         | "Forgot Password" right slot visibility |
| State             | VARIANT | "default"     | "default" or "active" (focus ring)      |

```js
// Figma MCP usage
const withLabelSet = await figma.importComponentSetByKeyAsync(
  "31498c87ad140feb48bc04bf2610be7ee1e81e03"
);
const defaultVariant = withLabelSet.children.find(c => c.name === "State=default");
const inst = defaultVariant.createInstance();
inst.resize(331, inst.height);
inst.primaryAxisSizingMode = "AUTO"; // CRITICAL — FIXED collapses to 10px

inst.setProperties({
  "Label#3098:6":   "First name",
  "Input#3097:0":   "Francisco",
  "State":          "default",        // "active" for focus state
  "Show Text#3052:0": false,
});
```

```tsx
// React usage
import { Label } from "@/components/ui/label"
import { Input } from "@/components/ui/input"

<div className="flex flex-col gap-3 w-full">
  <Label htmlFor="firstName">First name</Label>
  <Input id="firstName" type="text" placeholder="John" />
</div>

// With Forgot Password slot
<div className="flex flex-col gap-3 w-full">
  <div className="flex items-center justify-between">
    <Label htmlFor="password">Password</Label>
    <a href="/forgot-password" className="text-sm text-muted-foreground">
      Forgot Password
    </a>
  </div>
  <Input id="password" type="password" />
</div>
```

### Active state (focus ring) — from node 588:106
- Border: `1px solid var(--ring, #35c9b9)`
- Shadow: `0px 0px 0px 3px rgba(161,161,161,0.5)`
- Set via: `"State": "active"` in setProperties()

### Figma MCP gotchas
- Set `primaryAxisSizingMode = "AUTO"` on the WithLabel wrapper — FIXED collapses to 10px
- After placing, set Default child frame: `layoutGrow=1`, `layoutSizingHorizontal="FILL"`
- Always hide the "Buttons" child frame (search icon slot): `buttons.visible = false`
- Apply explicit white fill + #e4e4e7 border + cornerRadius 14 on Input instance — CSS vars don't resolve cross-file

---

## Stepper (onboarding flow)

| Variant    | Key                                        | State                         |
|------------|--------------------------------------------|-------------------------------|
| Stepper=1  | 895eb3f2c0c3dd1ad525f95f14d6d08beca96914  | Step 1 active (Company)       |
| Stepper=1.5| 6b180b5aba15f40fbc4a86e8e3552263e1023d14  | Between 1→2                   |
| Stepper=2  | c8863a506caa708a5560d11c3c29abcb8b8d1148  | Step 2 active (KYC)           |
| Stepper=2.5| 4aa36bf7fcf9bc1a7a78857038d0023638fb2871  | Between 2→3                   |
| Stepper=3  | 746dd671c46083f3b9fe3b31879191c57633d373  | Step 3 active (Collateral)    |

Library: 📚 Equity Earn Library, component SET key: 5c357019870e3b451060055a2a4a8291050d43a1

---

## shadcn Buttons component set

Component SET key: d055a734ff1f69b650e0b7cfa85750709e477931
Library: shadcn Jan 2026 community

| Variant                      | Key                                        |
|------------------------------|--------------------------------------------|
| Type=default, State=default  | d20b546e2bfdae2926dfdb1bbc540fca5e9252bb  |
| Type=outline, State=default  | 990dc920c1e1fb5b5fe5f35cb46bee49fd02e860  |
| Type=ghost, State=default    | c7fb860404a42436192a74293b01f8fe3ed17803  |
| Type=ghost, State=Selected   | 90e8b0170df79058c22bef18498d10ba1bd5b19e  |
| Type=link, State=default     | 8f2934f54965edb993bf19a373d89e21fbc0f947  |
| Type=icon, State=default     | ddbb19d3ea4ad682e364c0c0ac72e156f323e707  |
| Type=destructive, State=default | 6a3d99b3f1db4c04412a75e310fdfb328871fa08 |

### Button usage rules

| Component         | Usage                                                          |
|-------------------|----------------------------------------------------------------|
| Button (default)  | Primary CTA — always rounded-full w-full                      |
| Button (outline)  | Google SSO, secondary actions — add shadow-xs                 |
| Button (ghost)    | Nav tabs, icon-only actions — white fill override on dark bg  |
| Button (link)     | Inline text links (e.g. "Request to add a Company")           |
| Button (destructive) | Reject/delete in admin portal                             |
| Button (icon)     | Icon-only actions                                             |

Always hide `lucide/loader-circle` on instances: `loader.visible = false`

---

## Dropdown (shadcn Select)

Component SET key: 25e47b1d1fceac8f0f9dbad14ef30670a3eaad18
Library: shadcn Jan 2026 community

| Variant        | Key                                        |
|----------------|--------------------------------------------|
| State=default  | 491dc5eee67fb39007d2f27a314322312bf93fe7  |
| State=open     | d8d4dc89c8068e6fea55d79c66294b99cd0dcda7  |

---

## shadcn component → usage rules

| Component       | Usage rule                                                     |
|-----------------|----------------------------------------------------------------|
| Card            | Page sections, KPI tiles, REIT strategy cards                  |
| CardHeader      | Heading block inside Card                                      |
| CardTitle       | Screen/card title — always Text-2xl/Bold, Figtree 700 24px    |
| CardDescription | Subheading — muted-foreground                                  |
| Input           | Always use via With Label — never bare                         |
| Label           | Always paired with Input via htmlFor                           |
| Separator       | Horizontal dividers — pair with label overlay for "OR"         |
| Badge           | Status indicators — see colour token map in design-tokens.md   |
| Tabs            | Admin portal navigation, page-level switching                  |
| Dialog          | All modals — max-w-[657px] per PRD                             |
| Slider          | Loan amount, allocation mix, simulator inputs                  |
| Progress        | Health score bar — apply health colour via className           |
| Switch          | 2FA toggle, notification preferences                           |
| Select          | Dropdowns — use Dropdown component set above                   |
| Table           | All admin portal data tables                                   |
| Sheet           | Mobile-friendly side panels                                    |
| Sonner          | Toast notifications                                            |

---

## Icon library — Lucide React

```bash
npm install lucide-react
```

```tsx
// Default size: w-4 h-4 (16px) — matches shadcn defaults
import { Bell, Gift, CircleHelp, ArrowLeft, Eye, EyeOff } from "lucide-react"
<Bell className="w-4 h-4" />
```

### Figma → lucide-react name map (confirmed keys)

| Figma name           | lucide-react import | Figma component key                        |
|----------------------|---------------------|--------------------------------------------|
| lucide/search        | Search              | a2631588034f7d9ad53482a5746ea5f5361a72df  |
| lucide/loader-circle | Loader2             | 3fafd00f3f55d1cac0fa88ad4977d75d9370700c  |
| lucide/chevron-down  | ChevronDown         | 99da6e61fbdf5f83c2ef6b1737545b5266be0a5c  |
| lucide/check         | Check               | 118af1eaf5cd5069fa3c178a11e70333ed28f3d2  |
| lucide/arrow-right   | ArrowRight          | 1643b2ecffcc732c849841f289b61b43d8abe368  |
| lucide/bell          | Bell                | 52025e0dc6f9ebd1b952260bf2c6d36c4473d399  |
| hugeicons/gift-card  | Gift                | 17d5390f6d83413788d20937b8cdecf156a31b13  |
| remix/question       | CircleHelp          | 5a9052ae2030a83a042a89bd943414b14c43cd38  |

### Icon colour rule

On dark backgrounds (#1a1b1d nav bar): bind icon strokes to muted-foreground variable.
On light backgrounds: inherit currentColor from parent.
Never hardcode white or any colour directly on icon vector nodes.

### EquityEarn recommended icons

| Use case              | Import           |
|-----------------------|------------------|
| LTV / health warnings | AlertTriangle    |
| Profit up             | TrendingUp       |
| Profit down           | TrendingDown     |
| Loan / collateral     | Landmark         |
| Stock / portfolio     | BarChart2        |
| Settings              | Settings         |
| Admin / users         | Users            |
| Copy referral link    | Copy             |
| Show/hide password    | Eye / EyeOff     |
| Back navigation       | ArrowLeft        |
| Expand / collapse     | ChevronDown/Up   |
| Support chat          | MessageCircle    |
| Withdraw              | ArrowUpRight     |
| Notifications         | Bell             |
| KYC verified          | ShieldCheck      |
| Deploy / invest       | Rocket           |

---

## Colour binding rules

Never hardcode hex values. Always bind to variables.

| Variable               | Value    | Use on                                              |
|------------------------|----------|-----------------------------------------------------|
| muted-foreground       | #71717b  | Icon strokes/fills, placeholder text, captions      |
| Text/Secondary (EE)    | #737373  | Nav tab text, avatar text, button text on dark bg   |
| Stroke (EE)            | #dddddd  | Avatar/outline button border on dark bg             |
| foreground             | #09090b  | Primary text, headings, filled input values         |
| border                 | #e4e4e7  | Input borders, card borders, divider lines          |
| primary                | #14b8a6  | CTA buttons, focus rings, active states             |

```js
// Confirmed variable IDs (EquityEarn file)
const mutedFgVar       = await figma.variables.getVariableByIdAsync(
  "VariableID:022a84dc6b29f9af780898b12aa2c4bb4aa781d2/3003:5"  // muted-foreground
);
const textSecondaryVar = await figma.variables.getVariableByIdAsync(
  "VariableID:40002047:4301"  // Text/Secondary
);
const strokeVar        = await figma.variables.getVariableByIdAsync(
  "VariableID:40002051:3308"  // Stroke
);
```

---

## EquityEarn custom components

Build in components/equityearn/ — these are NOT shadcn primitives.

| Component       | Built from          | Purpose                                          |
|-----------------|---------------------|--------------------------------------------------|
| HealthScoreCard | Card + Progress     | 4-state colour logic (safe/warning/alert/critical)|
| LTVDisplay      | Card + Badge        | LTV % with colour-coded indicator                |
| InterestTicker  | Card                | Real-time accruing interest, updates every 1s    |
| ProfitTicker    | Card                | Real-time profit per REIT strategy               |
| StockCard       | Card + Badge        | Pledged stock value + performance                |
| AllocationSlider| Slider × 3          | REIT allocation mix with blended APY preview     |
| KYCStatusBadge  | Badge               | pending / approved / rejected states             |
| NavBar          | Nav bar component   | Wraps canonical EE Library nav bar               |
