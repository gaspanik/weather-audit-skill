# weather-audit — Figma AI-Readiness Forecast (Figma Design Agent edition)

Register this skill in Figma's custom skill feature, select a FRAME or SECTION on the canvas, and run it from the chat. It audits the frame's structure across 4 dimensions that determine how accurately an AI coding agent can implement it — layer naming, auto-layout usage, variables/tokens, and component structure — then delivers the verdict as a scored report *and* a weather forecast.

---

## What this is

AI coding agents read Figma files the same way they read code — structure matters as much as appearance. A frame full of `Frame 12`, hardcoded hex colors, and manually duplicated cards will produce worse code than one built with semantic names, auto-layout, tokens, and components, even if the two look identical on screen.

This skill makes that gap visible, and a little more fun to look at. It reads the node tree directly through Figma's Plugin API (no external MCP tools, no generated-code guessing), scores it across 4 dimensions out of 20, and reports the result as a compact score table — capped off with a weather forecast that matches how rough (or pleasant) the implementation job is about to be.

```
Select a FRAME / SECTION on canvas
        │
        ▼
Walk tree via Plugin API (evaluate_script)
        │
        ▼
Score 4 dimensions: naming / auto-layout / tokens / components
        │
        ▼
Overall score (X / 20)  →  Today's Figma Weather  →  Top 3 fixes
```

---

## The 4 dimensions

| # | Dimension | What is checked |
|---|-----------|-----------------|
| A | **Layer naming** | Meaningful role-based names vs. `Frame 123`, `Group 456`, duplicate siblings |
| B | **Auto-layout** | Proper padding/gap usage vs. absolute positioning and spacer frames |
| C | **Variables & tokens** | Bound variables (`boundVariables`) vs. hardcoded hex colors and fixed px values |
| D | **Component structure** | Instances and variants vs. manually duplicated frames |

Each dimension is scored 0–5. Total: **20 points** — mapped to one of five weather conditions, from ☀️ Mostly Sunny to 🌀 Typhoon Warning.

---

## Scope

- **Reads only.** This skill inspects the node tree and reports; it never renames layers, rewires variables, or changes the file in any way.
- **Target node types:** `FRAME` or `SECTION`. A `COMPONENT`/`COMPONENT_SET` selection still runs, but the report notes that component-level auditing is shallower here.
- Default output is a compact report (score table + weather + top 3 fixes) sized for a narrow chat panel. Say "show me the details" for the full per-dimension breakdown.

---

## Requirements

- **Figma (Design Agent / custom skill feature).** This skill only uses Figma's built-in Plugin API script execution tool (e.g. `evaluate_script`). No external MCP servers or API keys required — register `SKILL.md` as a custom skill and it runs.

---

## Repo structure

```
skills/
  weather-audit/
    SKILL.md          — skill definition to register in Figma's custom skill feature
    LICENSE
```

---

## Getting started

**Once published on Figma Community**, you'll be able to add this skill directly from the [AI Skills library](https://www.figma.com/community/ai-skills) — no download needed. Until then, register it from source:

**From source:** clone this repo and register the skill file yourself.

```bash
git clone https://github.com/gaspanik/weather-audit-skill
```

**1. Register `skills/weather-audit/SKILL.md`** in Figma's custom skill feature.

**2. Select a FRAME or SECTION** on the canvas.

**3. Run it from the chat**

```
/weather-audit
```

```
Check this frame's AI-readiness and give me today's forecast
```

```
このフレームのAI可読性を診断して、今日の天気を教えて
```

Larger frames take longer — auditing one section at a time is recommended.

---

## Example output

```
## 🌤️ Weather Audit Report

Overall score: 14 / 20

| Dimension           | Score | Rating |
|----------------------|-------|--------|
| A. Layer naming       | 4 / 5 | 🟢 Good |
| B. Auto-layout        | 3 / 5 | 🟡 Room for improvement |
| C. Variables & tokens | 2 / 5 | 🔴 Needs fixing |
| D. Component structure| 5 / 5 | 🟢 Good |

### Today's Figma Weather

## 🌤️ Sunny with Clouds

> The AI notices the clouds but is choosing to say nothing. A true professional.

### Top improvement priorities
1. ...
```

---

## Part of a larger set

This skill is one of 20 Figma-agent skills bundled in **KMRVID Figma Skills** — covering layer cleanup, contrast/accessibility checks, tokenization, component audits, ALT text suggestions, and more: [gaspanik.gumroad.com/l/kmrvid-figmaskills](https://gaspanik.gumroad.com/l/kmrvid-figmaskills)

---

Built by Masaaki Komori - [@cipher](https://x.com/cipher) · Skill for [Figma](https://www.figma.com/)
