# Build Prompt — paste this into Claude Code

> **How to use:** Start Claude Code inside the new project folder (the one with a
> `files/` subfolder full of materials and this `template/` folder). Fill in the
> five blanks in the **PROJECT BRIEF** below, then paste this entire file as your
> first message. Claude will read the materials and build the app.

---

## PROJECT BRIEF — fill these in

- **Subject:** `____________________`  (e.g. "Biology", "Math — Algebra", "History — WWII", "English Grammar")
- **Student name:** `____________________`  (shown in the header, e.g. "Maya")
- **Exam date:** `YYYY-MM-DD`  (drives the countdown timer)
- **Language & direction:** `____________________`  (e.g. "Hebrew, RTL" / "English, LTR" / "Arabic, RTL")
- **Grade / level:** `____________________`  (e.g. "9th grade", "high-school matriculation")

---

## INSTRUCTIONS FOR CLAUDE

You are building a static exam-prep website. **There is no build tooling** — pure
HTML/CSS/JS files opened directly in a browser. Follow the two reference documents
in this `template/` folder exactly:

- `template/APP-BLUEPRINT.md` — the design system, component patterns, and exact
  code snippets. The new app **must** match this so it looks and behaves like the
  original. Copy the CSS tokens, the multiple-choice pattern, the accordion, the
  hint toggle, the countdown, the scroll-reveal, and the score tracker verbatim and
  adapt only the content and accent colors.
- `template/CONTENT-TEMPLATE.md` — the structure for the source-of-truth content
  file you will produce in step 2.

### Step 1 — Read and understand the materials
Read **everything** in the `files/` folder: PDFs, photos of exams, answer keys,
syllabus, handouts. Images may be rotated, angled, or low quality — do your best to
transcribe them. Identify:
- The list of **topics** the exam covers, and roughly how many points each is worth
  (if a point breakdown exists in the materials, use it; otherwise estimate from how
  much space each topic gets).
- Any **formulas, definitions, key facts, dates, vocabulary, or rules** worth putting
  on a reference sheet.
- The **actual questions** from any past/official exam, with their correct answers
  (from the answer key if present, otherwise solve them yourself and show your work).

If something critical is ambiguous or missing (e.g. you can't tell the correct
answer to a past question, or the exam date wasn't given), **ask me** rather than
guessing. For minor gaps, make a reasonable call and note it.

### Step 2 — Write the source-of-truth content file
Create a single markdown file named after the subject (e.g. `content.md` or
`<subject>_summary.md`) following `template/CONTENT-TEMPLATE.md`. This holds ALL the
study content, the topic→points breakdown, the formula/fact sheet data, the study
plan, and the transcribed real exam questions. **Every HTML page renders from this
file** — keep them in sync. Show me this file and let me confirm before generating HTML.

### Step 3 — Generate the HTML pages
Following `APP-BLUEPRINT.md`, generate:

1. **`index.html`** — dashboard with:
   - Animated starfield + glow orbs background, the shared dark-glass theme
   - A countdown timer to the exam date
   - One **topic card** per topic, each with an "exam weight" bar sized to its points
   - A **quick-reference section** (formulas / key facts) grouped by topic
   - A **study plan** (week-by-week or day-by-day) linking to the day pages
   - An **interactive checklist** persisted in `localStorage`
   - Cards linking to `exam.html` and `formulas.html`

2. **`day1.html` … `dayN.html`** — one per study unit. Each has:
   - Theory cards explaining the concepts (in the student's language)
   - A set of **multiple-choice exercises** using the blueprint's MC pattern
     (`answerMC`), each with a **hint toggle** (`toggleHint`) and a full explanation
     in the feedback. Group exercises in **accordions** (`toggleEx`).
   - Difficulty levels low→high so it ramps up.

3. **`exam.html`** — two parts:
   - **Part 1:** the real exam questions transcribed in step 1, as MC with hints +
     full explanations. Show the original points per question.
   - **Part 2:** 5–8 fresh questions you invent in the same style and difficulty,
     same formulas/concepts, different numbers/scenarios.
   - A **live score tracker** that counts correct answers across the page.
   - If the student already took the exam and the score is known, add a celebratory
     banner; otherwise omit it.

4. **`formulas.html`** (skip if the subject has no formulas/facts worth a sheet) — a
   **printable** reference sheet: every formula/fact with **each variable or term
   explained in a full sentence and its unit** (e.g. *"I — this is the current,
   measured in amperes (A)"*). Two-column dense layout, `@media print` rules that
   force the 2-column layout and fit it onto ~2 A4 pages. Include a Print/PDF button
   on screen (hidden when printing).

### Step 4 — Wire it together & sanity-check
- Cross-link everything (dashboard ↔ day pages ↔ exam ↔ formulas, all with "back to
  home" nav).
- Pick an accent color per topic/day from the blueprint palette.
- Make sure all pages set the correct `lang` and `dir` for the chosen language.
- Open `index.html` mentally and verify the countdown, checklist, MC answering, hint
  toggles, and score tracker all work.

### Output discipline
- One concern at a time: show me the content file (step 2) before generating HTML.
- Write real files with the Write tool — don't dump full pages into chat.
- Tell me the file paths and a one-line summary of each when done.

---

## Notes / preferences (optional — edit or delete)
- Keep it encouraging and age-appropriate for the student.
- If materials are in one language but you want UI labels in another, ask me.
- Don't include answers/hints that give away the answer too directly — guide thinking.
