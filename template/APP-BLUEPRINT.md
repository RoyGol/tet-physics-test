# App Blueprint — design system & component patterns

This is the technical contract. Every app generated from this kit should reuse these
exact tokens, patterns, and snippets — adapting **content** and **accent colors**
only. Copy the code blocks verbatim and fill in the content.

---

## 1. Foundations

- **Stack:** pure HTML + inline `<style>` + inline `<script>`. No frameworks, no
  bundler, no package manager. Each page is fully self-contained so it opens
  directly in a browser.
- **Font:** [Heebo](https://fonts.google.com/specimen/Heebo) via Google Fonts (great
  Hebrew + Latin coverage). For other scripts pick an equivalent (e.g. Cairo/Tajawal
  for Arabic, Inter for Latin-only).
- **Direction:** set on `<html>` — `dir="rtl"` for Hebrew/Arabic, `dir="ltr"` for
  English etc. Also set `lang`. Everything below works in both directions.

```html
<!DOCTYPE html>
<html lang="he" dir="rtl">   <!-- or lang="en" dir="ltr" -->
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>…</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Heebo:wght@300;400;500;700;800;900&display=swap" rel="stylesheet">
<style> … </style>
</head>
<body> … </body>
</html>
```

---

## 2. Design tokens (CSS custom properties)

Paste this `:root` block into **every** page. These are the shared colors. Each
topic/day is assigned one accent (teal, blue, amber, purple, pink, green).

```css
:root {
  --bg: #060c1a;
  --bg2: #0b1428;
  --glass: rgba(255,255,255,0.04);
  --glass-border: rgba(255,255,255,0.08);
  --teal: #00e5c0;   --teal-dim: rgba(0,229,192,0.12);
  --amber: #ffbe3d;  --amber-dim: rgba(255,190,61,0.12);
  --pink: #ff6b9d;   --pink-dim: rgba(255,107,157,0.12);
  --blue: #4a9eff;   --blue-dim: rgba(74,158,255,0.12);
  --purple: #a78bfa; --purple-dim: rgba(167,139,250,0.12);
  --green: #34d399;
  --text: #e8f0fe;
  --text-dim: rgba(232,240,254,0.55);
  --text-muted: rgba(232,240,254,0.3);
}
* { margin:0; padding:0; box-sizing:border-box; }
html { scroll-behavior: smooth; }
body {
  font-family:'Heebo',sans-serif;
  background:var(--bg); color:var(--text);
  min-height:100vh; overflow-x:hidden;
  direction:rtl;            /* match <html dir> */
}
```

**Accent assignment convention** (from the original):
teal = topic 1 / Day 1 · blue = topic 2 / Day 2 · amber = topic 3 / Day 3 / the real
exam · purple = topic 4 / Day 4 · pink = final-week / thermal · green = experiment /
extra-practice. Pick consistently; it's how the student navigates by color.

---

## 3. Background: starfield + glow orbs

The **dashboard** (`index.html`) gets the animated canvas starfield + three blur
orbs. **Day/exam/formula pages** use orbs only (lighter — no canvas).

```html
<!-- index.html only -->
<canvas id="starfield"></canvas>
<div class="orb orb1"></div>
<div class="orb orb2"></div>
<div class="orb orb3"></div>
```
```css
#starfield { position:fixed; inset:0; z-index:0; pointer-events:none; }
.orb { position:fixed; border-radius:50%; filter:blur(120px); opacity:0.18; pointer-events:none; z-index:0; }
.orb1 { width:600px; height:600px; background:var(--teal); top:-200px; right:-150px; }
.orb2 { width:500px; height:500px; background:var(--amber); bottom:-150px; left:-100px; }
.orb3 { width:400px; height:400px; background:var(--purple); top:40%; left:30%; opacity:0.1; }
.wrap { position:relative; z-index:1; }   /* all content goes inside .wrap */
```
```javascript
// Minimal starfield — twinkling dots drifting upward.
const cv = document.getElementById('starfield');
if (cv) {
  const ctx = cv.getContext('2d');
  let stars = [];
  function resize(){ cv.width=innerWidth; cv.height=innerHeight;
    stars = Array.from({length:120},()=>({x:Math.random()*cv.width,y:Math.random()*cv.height,
      r:Math.random()*1.5+0.3,s:Math.random()*0.3+0.05,a:Math.random()})); }
  addEventListener('resize',resize); resize();
  (function loop(){ ctx.clearRect(0,0,cv.width,cv.height);
    for(const st of stars){ st.y-=st.s; if(st.y<0)st.y=cv.height;
      st.a+=(Math.random()-0.5)*0.05; st.a=Math.max(0.1,Math.min(1,st.a));
      ctx.globalAlpha=st.a; ctx.fillStyle='#cfe3ff';
      ctx.beginPath(); ctx.arc(st.x,st.y,st.r,0,7); ctx.fill(); }
    ctx.globalAlpha=1; requestAnimationFrame(loop); })();
}
```

---

## 4. Countdown timer (dashboard)

Counts down to the exam date. Put the target date where the build prompt's
**Exam date** goes.

```html
<div class="countdown" id="cd"></div>
```
```javascript
const EXAM = new Date('2026-05-26T08:00:00');  // ← exam date
function tick(){
  const d = EXAM - new Date();
  const el = document.getElementById('cd');
  if (d <= 0){ el.innerHTML = '<div class="cd-block"><div class="cd-num">🎉</div><div class="cd-label">בהצלחה!</div></div>'; return; }
  const days=Math.floor(d/864e5), hrs=Math.floor(d/36e5)%24, min=Math.floor(d/6e4)%60;
  const cell=(n,l)=>`<div class="cd-block"><div class="cd-num">${n}</div><div class="cd-label">${l}</div></div>`;
  el.innerHTML = cell(days,'ימים')+cell(hrs,'שעות')+cell(min,'דקות');  // localize labels
}
tick(); setInterval(tick,1000);
```
Style `.cd-block` as a glass card (`background:var(--glass); border:1px solid var(--glass-border); border-radius:16px; padding:20px 24px`), `.cd-num` large and in `--teal`, `.cd-label` small in `--text-dim`.

---

## 5. Topic cards with "exam weight" bars (dashboard)

One card per topic. The weight bar width = the topic's share of exam points, and it
**animates from 0 to its width** when scrolled into view (see §9).

```html
<div class="topic-card" data-color="teal" style="--weight:90%; animation-delay:0.05s"
     onclick="location.href='day1.html'" title="open study material">
  <span class="topic-num">topic 01</span>
  <span class="topic-icon">⚡</span>
  <div class="topic-title">Mechanical Energy <span style="font-size:12px;opacity:0.6">← study</span></div>
  <div class="topic-desc">Short description of what this topic covers.</div>
  <div class="topic-formulas">
    <span class="formula-tag">Eₕ = mgh</span>
    <span class="formula-tag">Eₖ = ½mv²</span>
  </div>
  <div class="topic-weight">
    <span class="weight-label">exam weight</span>
    <div class="weight-bar"><div class="weight-fill" style="--weight:90%"></div></div>
    <span class="weight-label">~23 pts</span>
  </div>
</div>
```
- The colored left/right edge comes from a `::before` strip tinted by `data-color`.
- `.weight-fill` animates: `width:0; transition:width 1s` → set to `var(--weight)` when the card gets the `.visible` class.
- A full-width feature card (link to `exam.html` / `formulas.html`) uses `style="grid-column:1/-1"`.

---

## 6. Multiple-choice exercise pattern  ⭐ (the core interaction)

This is the single most important reusable component. Used on every day page and on
`exam.html`. **Copy the CSS and the three JS functions verbatim.**

### HTML — one MC question ("part")
```html
<div class="mc-part"
     data-ok="✓ explanation shown when correct, e.g. the worked solution"
     data-err="✗ nudge shown when wrong, e.g. which formula to use">
  <div class="mc-sub-q">a. Optional sub-question label</div>
  <button class="hint-btn" onclick="toggleHint(this)">💡 hint</button>
  <div class="hint-body">The hint text — guides, doesn't give the answer.</div>
  <div class="mc-opts">
    <button class="mc-opt" data-c="0" onclick="answerMC(this)">wrong option</button>
    <button class="mc-opt" data-c="1" onclick="answerMC(this)">correct option</button>
    <button class="mc-opt" data-c="0" onclick="answerMC(this)">wrong option</button>
    <button class="mc-opt" data-c="0" onclick="answerMC(this)">wrong option</button>
  </div>
  <div class="mc-fb"></div>
</div>
```
- `data-c="1"` marks the correct option, `data-c="0"` the distractors.
- Multiple `.mc-part` blocks can live in one exercise; separate them with `<hr class="mc-divider">`.

### CSS
```css
.mc-part { margin-bottom:20px; }
.mc-sub-q { font-size:14px; color:var(--text-dim); margin-bottom:10px; font-weight:600; }
.hint-btn { display:inline-flex; align-items:center; gap:6px; padding:6px 14px; border-radius:8px;
  font-size:13px; font-weight:600; cursor:pointer; background:var(--amber-dim);
  border:1px solid rgba(255,190,61,0.25); color:var(--amber); margin-bottom:10px;
  user-select:none; font-family:'Heebo',sans-serif; }
.hint-body { display:none; background:var(--amber-dim); border:1px solid rgba(255,190,61,0.2);
  border-radius:10px; padding:12px 16px; font-size:13px; color:var(--amber); line-height:1.7; margin-bottom:12px; }
.hint-body.open { display:block; }
.mc-opts { display:flex; flex-direction:column; gap:8px; margin-bottom:10px; }
.mc-opt { padding:10px 16px; border-radius:10px; font-size:14px; font-weight:600;
  border:1px solid var(--glass-border); background:var(--glass); color:var(--text);
  cursor:pointer; text-align:right; transition:all 0.2s; font-family:'Heebo',sans-serif; }
.mc-opt:hover:not(:disabled) { background:rgba(255,255,255,0.07); border-color:rgba(255,255,255,0.2); }
.mc-opt:disabled { cursor:default; }
.mc-opt.mc-ok  { background:rgba(52,211,153,0.15); border-color:rgba(52,211,153,0.4); color:#34d399; }
.mc-opt.mc-bad { background:var(--pink-dim); border-color:rgba(255,107,157,0.4); color:var(--pink); }
.mc-opt.mc-rev { background:rgba(52,211,153,0.08); border-color:rgba(52,211,153,0.25); color:rgba(52,211,153,0.7); }
.mc-fb { font-size:13px; line-height:1.6; padding:10px 14px; border-radius:8px; margin-top:4px; }
.mc-fb.ok  { background:rgba(52,211,153,0.1); color:#34d399; border:1px solid rgba(52,211,153,0.2); }
.mc-fb.bad { background:var(--pink-dim); color:var(--pink); border:1px solid rgba(255,107,157,0.2); }
.mc-divider { border:none; border-top:1px solid var(--glass-border); margin:16px 0; }
```
> Note: `text-align:right` on `.mc-opt` suits RTL. For LTR use `text-align:left`.

### JS — the three functions
```javascript
function toggleEx(id){ document.getElementById(id).classList.toggle('open'); }

function answerMC(btn){
  const part = btn.closest('.mc-part');
  if (part.dataset.done) return;          // lock after first answer
  part.dataset.done = '1';
  const ok = btn.dataset.c === '1';
  part.querySelectorAll('.mc-opt').forEach(o => {
    o.disabled = true;
    if (o === btn) o.classList.add(ok ? 'mc-ok' : 'mc-bad');
    else if (!ok && o.dataset.c === '1') o.classList.add('mc-rev');  // reveal correct
  });
  const fb = part.querySelector('.mc-fb');
  fb.className = 'mc-fb ' + (ok ? 'ok' : 'bad');
  fb.innerHTML = ok ? ('✓ ' + (part.dataset.ok || '')) : ('✗ ' + (part.dataset.err || ''));
  if (window.onMCAnswer) window.onMCAnswer(ok);   // hook for the exam score tracker
}

function toggleHint(btn){
  const body = btn.nextElementSibling;
  body.classList.toggle('open');
  btn.textContent = body.classList.contains('open') ? '💡 hide hint' : '💡 hint';  // localize
}
```

---

## 7. Accordion exercise wrapper

Each exercise is a collapsible card opened by `toggleEx(id)`.

```html
<div class="exercise" id="ex1">
  <div class="ex-header" onclick="toggleEx('ex1')">
    <div class="ex-num">1</div>
    <div class="ex-question">Exercise title</div>
    <span class="lv-strip lv2">level 2</span>
    <span class="ex-chevron">▼</span>
  </div>
  <div class="ex-body"><div class="ex-content">
    <div class="ex-q">Question setup / given values…</div>
    <!-- one or more .mc-part blocks here -->
  </div></div>
</div>
```
```css
.exercise { background:var(--glass); border:1px solid var(--glass-border); border-radius:16px; margin-bottom:14px; overflow:hidden; }
.ex-header { padding:18px 24px; display:flex; align-items:center; gap:12px; cursor:pointer; user-select:none; }
.ex-num { width:32px; height:32px; border-radius:8px; background:var(--purple-dim); color:var(--purple); font-weight:800; display:flex; align-items:center; justify-content:center; }
.ex-question { flex:1; font-size:15px; font-weight:600; }
.ex-chevron { color:var(--text-muted); transition:transform 0.3s; margin-right:auto; }   /* margin-left:auto for LTR */
.exercise.open .ex-chevron { transform:rotate(180deg); }
.ex-body { max-height:0; overflow:hidden; transition:max-height 0.5s cubic-bezier(0.4,0,0.2,1); }
.exercise.open .ex-body { max-height:2400px; }
.ex-content { padding:0 24px 24px; }
```

---

## 8. Exam page score tracker (`exam.html`)

A sticky bar that counts correct answers as the student works. It hooks the
`answerMC` function via the `window.onMCAnswer` callback (see §6).

```html
<div class="score-bar">
  <span>score:</span>
  <span><span id="sc">0</span> correct of <span id="st">0</span></span>
  <span class="score-pct" id="sp">start answering!</span>
</div>
```
```javascript
let sc = 0, st = 0;
window.onMCAnswer = function(ok){
  st++; if (ok) sc++;
  document.getElementById('sc').textContent = sc;
  document.getElementById('st').textContent = st;
  document.getElementById('sp').textContent = Math.round(sc/st*100) + '% correct';
};
```
`exam.html` has **two sections**: Part 1 (real past-exam questions, show original
points per question) and Part 2 (fresh same-style questions). Use accent **amber**
for Part 1, **teal** for Part 2. If the real score is known, add a celebratory banner
at the top.

---

## 9. Scroll-reveal animation

Topic cards and section blocks fade/slide in when scrolled into view.

```css
.topic-card, .reveal { opacity:0; transform:translateY(24px); }
.topic-card.visible, .reveal.visible { animation: fadeUpIn 0.6s forwards; }
@keyframes fadeUpIn { to { opacity:1; transform:translateY(0); } }
.weight-fill { width:0; transition:width 1s ease 0.2s; }
.visible .weight-fill { width:var(--weight); }
```
```javascript
const io = new IntersectionObserver((entries)=>{
  for (const e of entries) if (e.isIntersecting){ e.target.classList.add('visible'); io.unobserve(e.target); }
}, { threshold:0.15 });
document.querySelectorAll('.topic-card, .reveal').forEach(el => io.observe(el));
```

---

## 10. Interactive checklist (dashboard) — persisted

Study checklist whose state survives reloads via `localStorage` (keys prefixed
`check_`).

```html
<label class="check-item"><input type="checkbox" data-key="topic1"> Review topic 1</label>
```
```javascript
document.querySelectorAll('.check-item input').forEach(box => {
  const key = 'check_' + box.dataset.key;
  box.checked = localStorage.getItem(key) === '1';
  box.addEventListener('change', () => localStorage.setItem(key, box.checked ? '1' : '0'));
});
```

---

## 11. Printable formula / fact sheet (`formulas.html`)

A standalone **light-themed, print-optimized** page. Skip it for subjects with
nothing formula-like; for fact-heavy subjects (history dates, biology terms,
vocabulary) adapt it to a "key facts / definitions" sheet with the same structure.

Requirements:
- **Each variable/term explained in a full sentence with its unit.** Example:
  *"I — this is the current (charge passing per second), measured in amperes (A)."*
- **Dense 2-column grid** of formula cards grouped by topic, color-coded to match the
  app accents.
- A **Print / Save-as-PDF button** on screen, hidden when printing.
- `@media print` rules that shrink typography (~9–11pt), reduce padding, and
  **force the 2-column layout** so it fits ~2 A4 pages.

⚠️ **Critical print gotcha (learned the hard way):** a bare
`@media (max-width:700px)` mobile rule that collapses grids to one column will ALSO
match the A4 print page and silently override your print layout (it wins by coming
later in the cascade), making it print as 3+ single-column pages. **Fix:** scope the
mobile rule to `@media screen and (max-width:700px)`, AND add explicit
`grid-template-columns:1fr 1fr !important` inside the `@media print` block.

```css
@media print {
  .toolbar { display:none; }                 /* hide the print button & nav */
  .sheet { box-shadow:none; max-width:none; margin:0; padding:8mm 9mm; }
  .grid       { grid-template-columns:1fr 1fr !important; }
  .rules      { grid-template-columns:1fr 1fr !important; }
  .footer-grid{ grid-template-columns:1fr 1fr !important; }
  /* + smaller font-sizes for .f-eq, .vars, .rule, etc. */
  @page { margin:0; size:A4; }
}
@media screen and (max-width:700px) {           /* note: screen-scoped! */
  .grid, .rules, .footer-grid { grid-template-columns:1fr; }
}
```

---

## 12. Shared nav (every non-dashboard page)

```html
<nav>
  <a href="index.html" class="nav-back">→ back to home</a>   <!-- ← arrow for RTL; → for LTR -->
  <span class="nav-title">Subject — Student</span>
  <span class="nav-badge">exam date</span>
</nav>
```
Fixed, blurred, glass bottom-border. Day pages can also show a small step
`progress-bar` of N segments.

---

## 13. File architecture

```
project/
├── index.html        # dashboard
├── day1.html …        # one per study unit
├── exam.html         # real + inspired questions, score tracker
├── formulas.html     # printable reference (optional)
├── content.md        # SOURCE OF TRUTH — all content lives here (see CONTENT-TEMPLATE.md)
├── files/            # raw materials (PDFs, exam photos) — NOT deployed
└── .gitignore
```

`content.md` is authoritative; the HTML renders it. When content changes, update the
markdown and the HTML together.

### `.gitignore` for deployment
Keep the heavy source materials and tool state out of the deployed site:
```gitignore
.DS_Store
Thumbs.db
.claude/
.planning/
.netlify/
# raw materials — kept locally, not deployed
files/*
!files/<any-image-actually-referenced-by-html>
```

---

## 14. Responsive & quality checklist

- [ ] `lang` + `dir` correct for the language; arrows/`text-align`/`margin-*:auto` flipped for LTR.
- [ ] Topic-card grid is responsive (`grid-template-columns:repeat(auto-fit,minmax(280px,1fr))`).
- [ ] Mobile breakpoint reduces nav/hero/section padding (and is `screen`-scoped if you also have print rules).
- [ ] Countdown, checklist, MC answering, hint toggles, accordions, score tracker all work.
- [ ] Every page links back home; dashboard links to every page.
- [ ] Formula sheet prints to ~2 A4 pages in 2 columns.
- [ ] No console errors; opens correctly as a `file://` (no server needed).
