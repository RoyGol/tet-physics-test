# Exam Prep App — Reusable Kit

This folder is a **recipe** for recreating the physics exam-prep app for *any*
subject, *any* language, and *any* exam date. It was extracted from the original
app built for a 9th-grade physics exam (Hebrew, RTL) — but nothing here is
physics-specific. You drop new study materials into a `files/` folder, hand these
documents to Claude Code, and get back a comparable app.

I built the original for one daughter's physics exam. Use this kit to spin up new
ones — e.g. biology, math, history, English — for each of your daughters.

---

## What you get each time

A self-contained static website (pure HTML/CSS/JS — no build tools, no server):

| Page | What it does |
|------|--------------|
| `index.html` | Dashboard: countdown to exam, topic cards with "exam weight" bars, quick-reference formula/fact sheet, study plan, interactive checklist (saved in the browser) |
| `dayN.html` | One study unit each — theory cards + multiple-choice practice with hints and full explanations |
| `exam.html` | Full exam practice: Part 1 = questions from the real past exam, Part 2 = fresh questions in the same style. MC + hints + explanations + a live score tracker |
| `formulas.html` (optional) | A printable single-/two-page reference sheet (formulas, key facts, definitions) optimized for A4 |

All pages share one dark "glass" visual theme, animate content in on scroll, and
work right-to-left or left-to-right depending on the language.

---

## How to use this kit (3 steps)

### 1. Start a new project folder
Create an empty folder for the new subject and copy this whole `template/` folder
into it. Then create a `files/` subfolder.

```
my-biology-app/
├── template/          ← copy this folder in
└── files/             ← create this, drop materials here
```

### 2. Add the study materials to `files/`
Put whatever you have into `files/`:
- The official exam / past exams (PDF or photos)
- The answer key, if you have one
- The syllabus or topic list
- Textbook pages, teacher handouts, WhatsApp photos of exercises — anything

More material = better questions. Even just one past exam works.

### 3. Tell Claude Code to build it
Open Claude Code in the new folder and paste the contents of
[`BUILD-PROMPT.md`](BUILD-PROMPT.md), filling in the blanks at the top
(subject, language, exam date, student name). Claude will:

1. Read everything in `files/`
2. Extract the content into a source-of-truth markdown (see [`CONTENT-TEMPLATE.md`](CONTENT-TEMPLATE.md))
3. Generate `index.html`, the day pages, `exam.html`, and the formula sheet
   following [`APP-BLUEPRINT.md`](APP-BLUEPRINT.md)

That's it. Open `index.html` in a browser to preview.

---

## The files in this kit

| File | Purpose |
|------|---------|
| [`README.md`](README.md) | This overview |
| [`BUILD-PROMPT.md`](BUILD-PROMPT.md) | **The thing you paste into Claude Code.** A fill-in-the-blanks prompt that drives the whole build. |
| [`APP-BLUEPRINT.md`](APP-BLUEPRINT.md) | The technical spec: design system, component patterns, exact HTML/CSS/JS snippets, file architecture. This is what makes every app look and behave the same. |
| [`CONTENT-TEMPLATE.md`](CONTENT-TEMPLATE.md) | The structure for the source-of-truth content file Claude fills in from your materials. Also fillable by hand. |

---

## Deploying (optional)

The original is deployed two ways — pick whichever you prefer:

- **GitHub + Hostinger**: `git init`, commit the HTML files, push to a GitHub repo,
  then pull on your Hostinger server. (See the `.gitignore` guidance in `APP-BLUEPRINT.md` —
  keep the heavy `files/` source out of the deployed site.)
- **Netlify**: `npx netlify-cli deploy --dir=. --prod` from the project folder.

Either way, only the `.html` files (plus any images they actually reference) need
to ship. The raw materials in `files/` stay local.
