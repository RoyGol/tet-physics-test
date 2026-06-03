# Content Template — the source of truth

Claude fills this in from the materials in `files/` (step 2 of `BUILD-PROMPT.md`).
You can also fill it in by hand. **Every HTML page renders from this file** — it is
the single source of truth. Save the filled-in copy in the project root as
`content.md` (or `<subject>_summary.md`).

Replace every `‹…›` placeholder. Delete sections that don't apply (e.g. **Formulas**
for a non-quantitative subject — but consider keeping it as "Key facts / Definitions").

---

## Meta

```yaml
subject:        ‹e.g. Biology — Cell Structure›
student:        ‹e.g. Maya›
grade_level:    ‹e.g. 9th grade›
exam_date:      ‹YYYY-MM-DD›        # drives the countdown
exam_name:      ‹official name of the exam/track›
language:       ‹e.g. Hebrew›
direction:      ‹rtl | ltr›
total_points:   ‹e.g. 100›
known_score:    ‹leave blank unless already taken; if known, e.g. 92›
```

---

## Topic → points breakdown

Drives the dashboard topic cards and their "exam weight" bars. List every topic with
its share of the exam. Use the materials' official breakdown if present; otherwise
estimate and say so.

| # | Topic | Points / weight | Accent color | Study-day page |
|---|-------|-----------------|--------------|----------------|
| 1 | ‹topic name› | ‹e.g. 23 pts› | teal   | day1.html |
| 2 | ‹topic name› | ‹e.g. 18 pts› | blue   | day2.html |
| 3 | ‹topic name› | ‹e.g. 23 pts› | amber  | day3.html |
| 4 | ‹topic name› | ‹e.g. 8 pts›  | purple | day4.html |
| … | … | … | … | … |

> Highlight the highest-value topic and the single most point-rich task type
> (e.g. "graph drawing = 12 pts") — these get extra emphasis and practice.

---

## Per-topic study content

Repeat this block for each topic. This becomes the theory cards + exercises on the
matching `dayN.html`.

### Topic ‹N›: ‹name›  ·  accent ‹color›

**What the student must understand**
- ‹key concept 1 — explained simply in the student's language›
- ‹key concept 2›
- ‹common mistakes / things examiners trip students on›

**Formulas / rules / key facts** (omit if N/A)
| Name | Expression | Variables & units (full sentences) |
|------|------------|------------------------------------|
| ‹Ohm's law› | ‹I = V / R› | ‹I — the current, in amperes (A); V — the voltage, in volts (V); R — resistance, in ohms (Ω)› |

**Worked example** (optional but valuable)
> ‹A solved problem with the steps shown.›

**Practice questions for this day** (each becomes an MC exercise)
For each, give: the question, 3–4 options with the correct one marked, a hint, and a
full explanation. Ramp difficulty from easy → hard.

1. **Q:** ‹question›
   - options: ‹A›, **‹B — correct›**, ‹C›, ‹D›
   - hint: ‹guides the approach without giving the answer›
   - explanation: ‹full worked solution for the feedback›
2. …

---

## Quick-reference sheet data (for `index.html` + `formulas.html`)

The condensed all-in-one reference. Group by topic. For each entry give the
expression/fact AND each term explained with its unit (this is the distinctive
feature of the printable sheet).

- **‹Topic›**
  - ‹formula or fact› — ‹each variable/term in a full sentence with its unit›
  - ‹rule / tip / common mistake›

**Constants** (if any): ‹e.g. g = 10 N/kg; c_water = 4200 J/(kg·°C)›
**Unit conversions** (if any): ‹e.g. 1 kg = 1000 g; 1 hour = 3600 s›

---

## Study plan

Drives the dashboard study-plan section. Week-by-week or day-by-day, each entry
linking to its day page.

| When | Focus | Page | Accent |
|------|-------|------|--------|
| ‹Day 1 / Week 1› | ‹topic› | day1.html | teal |
| ‹Day 2› | ‹topic› | day2.html | blue |
| … | … | … | … |
| ‹Final days› | ‹full exam practice› | exam.html | amber |

---

## Real exam questions (for `exam.html` — Part 1)

Transcribe the actual past/official exam from `files/`. One block per question.
Get the correct answer from the answer key if present; otherwise solve it and show
your work.

### Question ‹N› — ‹short title›  ·  ‹original points›
- **Setup / given:** ‹the question text and given values›
- **Part a:**
  - options: ‹A›, **‹B — correct›**, ‹C›, ‹D›
  - hint: ‹…›
  - full explanation: ‹worked solution›
- **Part b:** ‹…if multi-part…›

> Repeat for every question on the real exam.

---

## Inspired questions (for `exam.html` — Part 2)

5–8 fresh questions Claude invents in the same style/difficulty as the real exam —
same concepts and formulas, different numbers/scenarios. Same structure as above
(question, options with correct marked, hint, full explanation). Label them so the
student knows these are practice, not the original.

---

## Tone & notes

- Language/reading level: ‹e.g. encouraging, age 14, Hebrew›
- Anything to emphasize or avoid: ‹…›
- Source quality caveats: ‹e.g. "exam photos 3–5 were rotated; Q2 reconstructed"›
