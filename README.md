# Poison Ivy Identification Quiz

An interactive, single-file HTML quiz that teaches users to distinguish poison ivy from common look-alike plants. Built for hikers, educators, parents, and anyone who spends time outdoors.

---

## Summary

The quiz presents 7 questions, each showing two side-by-side plant photos — one poison ivy, one a look-alike. The user clicks the plant they believe is poison ivy. The card highlights green (correct) or red (incorrect), an explanation appears on each card, and a feedback banner summarizes the key lesson. A final score screen includes a reference tip sheet of real-world identification rules.

All logic, styling, and content are contained in a single `index.html` file with no external dependencies, frameworks, or build tools required.

---

## Features

- **7 curated questions** covering the most common misidentifications:
  - Poison ivy vs. Virginia creeper (5 vs. 3 leaflets)
  - Poison ivy vs. wild raspberry/blackberry (thorns)
  - Poison ivy vs. fragrant sumac (closest harmless relative)
  - Poison ivy hairy vine vs. bittersweet vine
  - Poison ivy seedling vs. wild strawberry
  - Poison ivy berries vs. elderberry
  - Fall-colored poison ivy vs. fall Virginia creeper

- **Click-to-answer cards** with rounded rectangle containers, hover effects, and smooth animations (green pulse on correct, red shake on incorrect)
- **Per-card explanation text** that reveals after answering — explains both why the correct answer is right and why the wrong answer is wrong
- **Feedback banner** beneath the cards summarizing the key identification rule for each question
- **Progress bar** showing advancement through the quiz
- **Live score counter** updating after each answer
- **Final score screen** with emoji rating, performance message, and a reference tip sheet of real identification rules
- **Restart button** to replay the quiz from the beginning
- **Image fallback** — if a photo fails to load, a styled placeholder renders instead of a broken image
- **Responsive layout** — two-column card grid collapses to single column on narrow screens
- **No dependencies** — pure HTML, CSS, and vanilla JavaScript; opens directly in any browser

---

## How to Replicate

### File Structure

```
Poison Ivy Quiz/
└── index.html
```

Everything lives in one file. No npm, no build step, no server required.

### Step 1 — Create the HTML shell

Set up a standard HTML5 document with a `<style>` block in `<head>` and a `<script>` block at the end of `<body>`. All CSS and JS are inline.

### Step 2 — Define the question data

In the `<script>` block, create a `questions` array. Each question object has:

```js
{
  question: "String — the prompt shown at the top of the slide",
  choices: [
    {
      label: "A",               // display label (unused visually but useful for logic)
      isPoison: true,           // true = this is the poison ivy option
      image: "URL",             // direct image URL (see Image Sourcing below)
      shortDesc: "String",      // 1–2 sentence description shown before answering
      resultDesc: "String",     // explanation revealed after the user answers
      credit: "String"          // attribution for the image source
    },
    { /* second choice, isPoison: false */ }
  ],
  feedbackCorrect: "String",    // shown in the banner when user picks correctly
  feedbackIncorrect: "String"   // shown in the banner when user picks incorrectly
}
```

Randomizing which choice appears on the left vs. right is optional but recommended to prevent pattern recognition. In this implementation the order is manually set per question to ensure visual variety.

### Step 3 — Render questions dynamically

Use a `renderQuestion()` function that reads `questions[currentQ]` and builds the slide HTML via `innerHTML`. Key elements to render:

- A `.question-label` and `.question-text` at the top
- A `.choices` grid containing two `.choice` cards
- Each card contains an `<img>`, a `.choice-short-desc`, and a hidden `.choice-result-desc`
- A `.feedback-banner` div (hidden until answered)
- A Next/Results `<button>` (disabled until answered)

### Step 4 — Handle answer selection

The `selectChoice(idx)` function:
1. Guards against double-answering with an `answered` boolean flag
2. Checks `choices[idx].isPoison` to determine correctness
3. Increments score if correct
4. Adds `.correct` or `.incorrect` CSS classes to the clicked card
5. Always adds `.correct` to whichever card `isPoison === true` (so the right answer is always revealed)
6. Shows `.choice-result-desc` via CSS (the class change triggers `display: block`)
7. Populates and shows the `.feedback-banner`
8. Enables the Next button

### Step 5 — Progress and navigation

- `currentQ` and `score` are module-level integers
- `nextQuestion()` increments `currentQ` and calls `renderQuestion()`, or calls `showScore()` on the last question
- The progress bar width is `(currentQ / total) * 100 + '%'`

### Step 6 — Score screen

`showScore()` hides the slide and progress bar, shows a `#score-screen` div, and sets copy based on percentage thresholds (100%, ≥85%, ≥70%, ≥50%, below 50%).

### Step 7 — Restart

`restartQuiz()` resets `currentQ = 0` and `score = 0`, restores hidden elements, and calls `renderQuestion()`.

---

## Image Sourcing

All images must be openly licensed for educational use. Recommended sources:

| Source | License | How to find images |
|---|---|---|
| [iNaturalist](https://www.inaturalist.org) | CC-BY or CC-BY-NC | Search a species, open an observation, right-click the photo to get the CDN URL (`inaturalist-open-data.s3.amazonaws.com/photos/...`) |
| [Wikimedia Commons](https://commons.wikimedia.org) | CC-BY, CC-BY-SA, or Public Domain | Search the species name, click an image, use the direct file URL (`upload.wikimedia.org/wikipedia/commons/...`) |
| [USDA PLANTS](https://plants.usda.gov) | Public Domain | Search a species and look for linked image galleries |

**Important:** Always verify the license on each individual photo. CC-BY requires attribution; CC-BY-NC prohibits commercial use. This project includes a blanket attribution footer linking to both sources.

Use direct image URLs (hotlinking). For a production deployment, download and self-host the images to avoid broken links if upstream sources change.

Image dimensions of roughly **600×450px (4:3 ratio)** work best with the card layout. The CSS uses `object-fit: cover` so any aspect ratio will fill the card cleanly, but very tall or very wide images may crop awkwardly.

---

## Design Decisions

**Single-file architecture** — Eliminates setup friction. Anyone can open the file in a browser with no installation, making it ideal for sharing, embedding in a course, or distributing offline.

**Dark green color theme** — Reinforces the outdoors/nature context. Primary background is `#1a2e1a`; card backgrounds are `#22362a`; accent greens use Material Design's green palette (`#388e3c`, `#66bb6a`, `#a5d6a7`).

**Always reveal the correct answer** — After a wrong guess, the correct card still highlights green. This ensures the quiz is educational even when the user guesses wrong, rather than just penalizing them.

**Explanations on both cards** — Each card shows a different explanation: the correct card explains why it IS poison ivy, the incorrect card explains why it is NOT. This double-sided learning reinforces identification in both directions.

**No randomization of question order** — Questions are ordered by difficulty and conceptual progression (easiest/most iconic first, subtler distinctions later). Randomizing could be added by shuffling the `questions` array in `restartQuiz()`.

**CSS-driven state** — Card state (correct/incorrect/disabled) is handled entirely by adding CSS classes. No direct style manipulation in JS. This keeps the logic clean and the animations purely declarative.

---

## Extending the Quiz

**Add more questions:** Add another object to the `questions` array following the schema above. The progress bar and score screen update automatically.

**Add more look-alikes per question:** The `.choices` grid supports more than 2 cards — change the grid to `repeat(3, 1fr)` and add a third choice object. Update `selectChoice()` to handle `n` choices.

**Randomize answer position:** In `renderQuestion()`, shuffle `q.choices` before rendering so poison ivy isn't always on the same side.

**Add a timer:** Add a countdown per question using `setInterval`, clearing it on answer selection. Display remaining seconds near the question text.

**Track wrong answers for review:** Push incorrect question indices to a `missed[]` array and show a "Review missed questions" section on the score screen.

**Localize or translate:** All user-facing strings are in the `questions` array and a small set of hardcoded labels. Extracting them to a `strings` object makes translation straightforward.

---

## Attribution

Plant photos sourced from:
- [iNaturalist](https://www.inaturalist.org) — open data under CC-BY and CC-BY-NC licenses
- [Wikimedia Commons](https://commons.wikimedia.org) — images under CC-BY, CC-BY-SA, and Public Domain

Educational content informed by:
- USDA Forest Service poison ivy identification guides
- University extension publications on toxic plants of North America
