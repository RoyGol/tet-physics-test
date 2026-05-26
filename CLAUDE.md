# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Purpose

A static Hebrew-language physics exam prep website for a 9th-grade student (גולי) preparing for the Scientific-Technological Reserve (עתודה מדעית טכנולוגית) exam on **26 May 2026**. No build tools, no framework, no package manager — pure HTML/CSS/JS files served directly in a browser.

## Developing

Open any `.html` file directly in a browser. No server required. To preview changes quickly:

```
start index.html      # Windows
open index.html       # macOS
```

## File Architecture

| File | Role |
|------|------|
| `index.html` | Main dashboard: countdown, 6 topic cards, formula sheet, study plan, interactive checklist |
| `day1.html` | Day 1 study material — Mechanical Energy (Eₕ, Eₖ, conservation law) |
| `day2.html` | Day 2 — Hydroelectric power station, energy conversions, efficiency |
| `day3.html` | Day 3 — Electric circuits, Ohm's law, series vs. parallel |
| `day4.html` | Day 4 — Power (P=VI) and thermal energy (Q=mcΔT) |
| `פיזיקה_סיכום_ותוכנית_לימודים.md` | Source-of-truth: all physics content, formulas, study schedule, and point breakdown |
| `files/` | Source PDFs (official exam, answer key, 2018 exam) + student's WhatsApp photos of exercises |

The `index.html` topic cards and study plan day-cards link to `day1.html`–`day4.html` via `onclick="location.href='dayN.html'"`.

Checklist state persists in `localStorage` — keys are prefixed `check_`.

## Design System

All pages share the same CSS custom properties (copy from any file's `:root`):

| Token | Value | Use |
|-------|-------|-----|
| `--bg` | `#060c1a` | Page background |
| `--glass` | `rgba(255,255,255,0.04)` | Card fills |
| `--teal` | `#00e5c0` | Primary accent (Day 1, formulas) |
| `--amber` | `#ffbe3d` | Day 3 accent |
| `--pink` | `#ff6b9d` | Final-week accent |
| `--blue` | `#4a9eff` | Day 2 accent |
| `--purple` | `#a78bfa` | Day 4 accent |
| `--green` | `#34d399` | Topic 6 accent |
| `--text-dim` | `rgba(232,240,254,0.55)` | Secondary text |

The animated starfield (`<canvas id="starfield">`) and three blur orbs appear in `index.html`. Day pages use orbs only (no canvas) to keep them lighter.

All pages are **RTL Hebrew** (`<html lang="he" dir="rtl">`). Font: Heebo (Google Fonts).

## Scroll-reveal pattern

Topic cards and week blocks animate in via `IntersectionObserver`. Add the `visible` class in JS; CSS handles the `fadeUpIn` keyframe. Weight bars animate their `width` from `0` to `var(--weight)` when `.visible` is set.

## Content source

When updating physics content, treat `פיזיקה_סיכום_ותוכנית_לימודים.md` as the authoritative reference. The HTML pages render this content; they should stay in sync with it.

The exam point breakdown (from `פיזיקה_סיכום_ותוכנית_לימודים.md`): Question 4 = 29 pts (highest), graph drawing = 12 pts, total ≈ 100 pts.
