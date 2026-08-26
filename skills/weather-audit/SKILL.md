---
name: weather-audit
description: >-
  Audits the selected frame's AI-readiness across 4 dimensions (layer naming,
  auto-layout, variables/tokens, component structure) by inspecting the node
  tree directly through the Plugin API, and delivers the verdict as a compact
  scored report plus a weather forecast — score table, today's Figma weather,
  and top 3 improvements, with full details available on request. Figma
  Design Agent edition. Part of KMRVID Figma Skills, a 20-skill bundle
  covering AI-slop-resistant page generation, multi-layout exploration,
  layer cleanup, accessibility checks, and tokenization:
  gaspanik.gumroad.com/l/kmrvid-figmaskills
---

# Weather Audit — Figma AI-Readiness Forecast (Figma edition)

**Ver:** ver.202608261517

Evaluate whether the selected frame is structured in a way that allows AI to implement it accurately — then deliver the verdict as a weather forecast.

**Output language:** All user-facing output is English. If the user writes in another language, follow their language instead.

**Environment:** This edition runs inside Figma's agent environment. The target comes from the current canvas selection, and all inspection goes through the Plugin API script execution tool (e.g. `evaluate_script`). Because we can read the file directly, variable usage is checked via each node's `boundVariables` — more accurate than inferring from generated code.

---

## Step 1: Resolve the target

Use the current canvas selection. If nothing is selected, ask the user to select the frame to audit. A `FRAME` or `SECTION` is the intended target; a `COMPONENT`/`COMPONENT_SET` selection still works, but note in the report that component-level auditing is shallower here (this skill audits page/frame structure).

## Step 2: Gather data

In one script execution, walk the tree from the root (record but do not descend into `INSTANCE` nodes beyond noting their presence and main component name) and collect per node:

- `id`, `type`, `name`
- `layoutMode`, and for auto-layout nodes: `itemSpacing`, the four paddings
- For nodes with `fills`/`strokes`: each visible SOLID paint's color and whether the paint has a bound variable (`paint.boundVariables?.color`)
- For `TEXT` nodes: `textStyleId` (or `getStyledTextSegments(['textStyleId', 'fontSize'])` when mixed), and whether `fontSize` is variable-bound (`node.boundVariables?.fontSize`)
- Whether spacing/padding properties are variable-bound (`node.boundVariables` keys like `itemSpacing`, `paddingLeft`, ...)
- Sibling-name duplication and structural similarity between sibling frames (same child-type sequence ≒ componentization candidate)

Keep it to counts and representative examples — the report needs totals and a few node names, not the full dump.

## Step 3: Analyze 4 dimensions

Score each dimension 0–5: **5** no issues / **3–4** minor (1–2 items) / **1–2** needs improvement (3+ items) / **0** almost entirely unaddressed.

### A. Layer naming
- Auto-generated names remain (`Frame 123`, `Group 456`, bare `Frame`/`Ellipse`)
- Sibling elements sharing the same name
- Generic names that don't convey role (`Images`, `Text`, `Icon`)
- Text node names that are just the raw text content (problematic for long strings)

### B. Auto-layout usage
- Children absolutely positioned (`layoutMode === 'NONE'` on containers that visually read as rows/columns)
- Empty spacer frames used instead of gap
- Spacing managed by manual position rather than padding/gap

### C. Variables and tokens *(checked via `boundVariables`, not generated code)*
- Fill/stroke colors hardcoded — count paints with vs. without `boundVariables.color`
- Font sizes unbound and text styles unapplied (`textStyleId` empty and no `fontSize` binding)
- Spacing/padding values unbound on auto-layout nodes

### D. Component and instance structure
- Repeated similar-structure frames that are not componentized (structural-similarity candidates from Step 2)
- Repeated structures present as `FRAME` copies instead of `INSTANCE`
- State variations as separate frames rather than variants

## Step 4: Output the compact report

The chat window is narrow — keep the default report short. Score table + weather + top 3 improvements only:

---

## 🌤️ Weather Audit Report

**Target:** [Node name] (`nodeId`)

### Overall score: X / 20

| Dimension | Score | Rating |
|------|--------|------|
| A. Layer naming | X / 5 | 🟢 Good / 🟡 Room for improvement / 🔴 Needs fixing |
| B. Auto-layout | X / 5 | ” |
| C. Variables & tokens | X / 5 | ” |
| D. Component structure | X / 5 | ” |

### Today's Figma Weather

Based on the total score, select one weather and deliver it with its comment, formatted like a weather broadcast:

| Score | Weather | Comment |
|-------|---------|---------|
| 18–20 | ☀️ Mostly Sunny | "So this is what a real Figma file looks like…" — the AI whispers, then skips to work humming cheerfully. |
| 13–17 | 🌤️ Sunny with Clouds | The AI notices the clouds but is choosing to say nothing. A true professional. |
| 8–12 | ☁️ Overcast | The AI exhales slowly and rolls up its sleeves. "I've seen worse," it lies. |
| 3–7 | 🌧️ Heavy Rain | The AI is staring into the void. Please bring snacks. Completion time: unknown. |
| 0–2 | 🌀 Typhoon Warning | Mandatory evacuation in effect. The AI has already left the building. This file may be cursed — approach with caution and low expectations. |

Format the block like this (adapt language to the conversation):

```
## [weather emoji + weather name]

> [comment]
```

### Top improvement priorities (top 3)

1. **[Location]** — the issue and the specific action to take in Figma, one sentence each
2. …
3. …

(One sentence on what's already good, or an overall assessment)

If you'd like to see the full breakdown or per-dimension details, say "show me the details."

---

**On request** (e.g. "show me the details"), output the full breakdown: per-dimension checklists with the specific nodes affected, strengths worth keeping, and the complete improvement list in the Issue/Impact/Suggestion format. Don't volunteer this up front. The weather block is not repeated in the detailed view.

---

## Error handling

- No selection: ask the user to select the target frame
- Script execution error: report it and ask the user to retry (the file may have been edited mid-scan)
- Very large trees: if a single walk is too slow or errors, walk top-level sections one at a time and merge counts
