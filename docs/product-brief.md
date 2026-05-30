# German Coach App — Product & UX Brief
> Living document. Last updated after UX brainstorm session 1-4.
> For internal use.

---

## The problem we're solving

Most people who want to learn German don't fail because of bad resources — they fail because they stop showing up. The two biggest killers are **inconsistency** and **procrastination**.

> "This app isn't really about German. It's about making it easy to show up every day and actually do the thing. German is the subject. Consistency is the product."

Writing is the most neglected skill in language learning. Yet writing and speaking share the same cognitive mechanism — if you become good at structured writing, your speaking improves too. This app makes daily writing the non-negotiable ritual.

---

## Who is this for

| User | Description |
|------|-------------|
| 🧑 Self-learner | Learning alone, no teacher. Needs structure, daily guidance, and motivation to keep going. |
| 🎓 Class supplement | Already taking classes. Uses this for extra daily practice and reinforcement between lessons. |

---

## Core learning philosophy

Based on how language acquisition actually works — a word needs to be encountered many times across multiple modalities before it becomes subconscious.

> A word should be written, seen, and said 20 times each before it truly belongs to you.

This is the foundation of the word mastery system.

| 📖 See it | ✍️ Write it | 🗣️ Say it |
|-----------|------------|----------|
| AI embeds it in feedback and tasks | User produces it in writing tasks | Speaking practice (v2) |
| Target: 20 exposures | Target: 20 correct usages | Target: 20 spoken uses |

---

## The core loop (v1)

```
Notification → Open app → Write task → AI corrects → Words extracted → Pool grows → repeat
```

> The submission IS the proof of showing up. No checkbox — you have to actually write something.

---

## Platform decision

**Mobile first.** The daily ritual (notification → quick task → done) is a mobile behavior pattern. Push notifications — the primary re-engagement mechanic — only work natively on mobile.

The writing experience is the one area where desktop feels more comfortable. So:
- Mobile → primary experience, notifications, streaks, quizzes
- Desktop/browser → secondary, same account, synced data, better for longer writing

---

## Navigation structure

Mobile bottom nav — 4 tabs:

| Tab | Screen | Reason |
|-----|--------|--------|
| ✍️ Write | Daily task + writing | Hero screen — the app's core action |
| 📚 Library | Word pool + archive | Personal vocabulary collection |
| 📈 Progress | Stats + writing history | Motivation and reflection |
| 👤 Profile | Level, settings, milestones | User control |

**Write is the first tab** — not a dashboard. Opening the app should put the user one tap away from writing.

---

## Level system

Users select their level at onboarding (A1 → C1), based on CEFR standards.

| Level | Approximate vocabulary |
|-------|----------------------|
| A1 | ~500 words |
| A2 | ~1,000 words |
| B1 | ~2,000 words |
| B2 | ~4,000 words |
| C1 | ~8,000 words |

### Two-variable level architecture
```
user.level = "B1"           // set at onboarding, visible to user
user.effectiveLevel = "B1"  // what AI actually uses, can adapt silently
```

This allows v2 adaptive difficulty without a refactor. If the user consistently makes A2-level mistakes on B1 tasks, `effectiveLevel` drifts down silently — no shame, just adaptation.

### Level progression
Level upgrades are earned, not manually selected. When a user archives enough words to cover their current CEFR range, the app recognises it:

> "You've mastered 2,000 words — the full B1 vocabulary range. Ready to move to B2?"

---

## The Write screen — 5 states

This is the most important screen in the app. Every design decision here should serve one goal: **make writing feel natural and focused.**

### State 1 — Task presented
```
What's visible:   Task title, description, difficulty indicator, "Start writing" CTA
What's hidden:    Word pool, navigation, stats, yesterday's task
```
One job, one screen. The user should feel like there's only one thing to do.

> Open question: Does the full task reveal before or after tapping "Start writing"? 
> Revealing after creates a small moment of commitment.

### State 2 — Writing (focused mode)
```
What's visible:   Task title (small, top), writing area (dominant), word count, submit button
What's hidden:    Bottom navigation (hidden in this state), word pool (locked)
```
The app steps back and gives the user space. No distractions.

**Anti-cheat mechanics:**
- Word pool is inaccessible during writing
- Copy-paste from external sources: TBD (see open questions)
- Leaving the app: task **pauses**, not deleted. User returns to a "Welcome back, your task is waiting" screen
- Frequent app-switching tracked as a **focus score** — visible but not punishing

### State 3 — AI processing
This pause (3-5 seconds) should feel earned, not empty.

Progressive messages instead of a generic spinner:
```
"Reading your writing..."
"Checking your grammar..."
"Finding new words for your library..."
```

### State 4 — Feedback received
Structure:
```
✓ Corrected version     → clean rewrite with color coding
⚠ Mistakes explained   → max 3, clear language, not harsh
💡 Vocabulary tip       → one new thing
🌟 Encouragement        → genuine, specific to their writing
```

**Color coding system for corrections:**
```
Neutral/dark   → correct, kept as written
Orange         → wrong word, right idea (semantic error)
Red            → structural or syntax error
```

The user sees what they got RIGHT as well as what's wrong. Red is a map, not a grade.

### State 5 — Words extracted
```
"3 new words added to your library"
[ Fehler ]  [ Entschuldigung ]  [ trotzdem ]
            → View library      → Done
```
This moment should feel like **collecting something valuable.** The writing produced something tangible.

---

## The Word Library

### What it is
A personal dictionary built entirely from the user's own mistakes and new words encountered during corrections. Words learned through your own writing stick better than generic lists.

### Word entry structure
Each word tracks mastery across three modalities:

```
word: "Entschuldigung"
  write_count: 7/20     ← used correctly in writing tasks
  see_count: 4/20       ← appeared in AI feedback or task
  say_count: 0/20       ← v2
  status: "active"      ← active | archived
  added_date: ...
  last_seen: ...
  synonyms: []          ← populated after write_count hits 10/20
```

### The 20-repetition target
- **Write it** — AI injects pool words naturally into generated tasks (max 2 per task)
- **See it** — AI uses pool words in corrections and feedback
- Synonyms are introduced only after the original word reaches 10/20 on write_count — not before

### Word injection algorithm
When the AI generates a daily task, it:
1. Reads the user's active word pool
2. Selects 1-2 words with the lowest recent exposure
3. Naturally embeds them into the task context
4. User encounters them subconsciously — that's the mechanism

### Mastery and archiving
When a word reaches 20/20 on all active modalities it graduates:
```
Status: active → archived
```
Micro-celebration screen:
> "You've written *Entschuldigung* correctly 20 times. It's yours now."

### Milestones
```
10 words archived    → first trophy
50 words archived    → bronze
100 words archived   → silver  (roughly A1 complete)
500 words archived   → gold
1,000 words archived → platinum
2,000 words archived → B1 complete — level up prompt
```

### Library screen layout
```
Active pool    → words in progress, sorted by priority
Archived       → mastered words, searchable
Stats bar      → X active / Y archived / Z total
```

> Open question: Should the library be browsable freely, or only surface words during quizzes and tasks?
> Current lean: freely browsable — it's the user's personal dictionary, they should own it.

---

## The Progress screen

### Philosophy
Most apps do **micro motivation** — streaks, daily scores, guilt for missing days. This app does **macro motivation** — perspective on how far the user has come.

The most motivating thing you can show a language learner is their own growth in their own words.

### Screen structure
```
┌─────────────────────────────┐
│  Your German journey        │
│  Day 47                     │
├─────────────────────────────┤
│  This month                 │
│  Tasks completed    18/30   │
│  Mistakes per task  ↓ 40%   │
│  Words learned      +35     │
│  Avg task length    ↑ 65→95w│
├─────────────────────────────┤
│  Your writing then vs now   │
│                             │
│  Day 1:                     │
│  "Ich habe ein Buch..."     │
│                             │
│  Today:                     │
│  "Gestern habe ich ein      │
│   interessantes Buch..."    │
├─────────────────────────────┤
│  Word milestones            │
│  ░░░░░░░░░░ 47/100 🏆       │
└─────────────────────────────┘
```

### Dashboard teaser card
A small progress card lives on the dashboard:
> "↓ 40% fewer mistakes this month"

Taps through to the full Progress screen. Serves both the motivated user (proud, wants more) and the struggling user (needs a reason to continue).

---

## Re-engagement logic

The app tracks usage frequency and responds with **progressively personal** interventions — never guilt, always perspective.

```
Miss 2 days      → "Your streak is still alive. Write today?"
Miss 4-5 days    → "You've learned 47 words. Don't let them get rusty."
Miss 1 week      → Shows day 1 writing vs most recent writing
Miss 2+ weeks    → Soft re-onboarding: "Welcome back. Just one sentence today."
```

### The "one sentence" re-entry
When a user has been away 2+ weeks, the normal task feels like a mountain. The re-engagement task is deliberately tiny:
```
Normal task        → "Write a paragraph about your weekend plans"
Re-engagement task → "Write one sentence about something you did today"
```
Same mechanic. Fraction of the effort. The streak doesn't reset — it pauses.

---

## Open questions (move to GitHub Issues)

- [ ] Should copy-paste be blocked during writing tasks? Risk: too punishing on mobile. Alternative: flag it in the focus score.
- [ ] Does the full task reveal before or after tapping "Start writing"?
- [ ] Should the Library be freely browsable or quiz-gated?
- [ ] User accounts in v1 or local/anonymous storage first?
- [ ] Which AI provider — keep Groq, move to Claude/OpenAI, or make it swappable?
- [ ] Stack — vanilla HTML/JS or React + proper backend?
- [ ] What does the reminder notification say exactly — copy needed.

---

## Sessions completed

| Session | Topic | Status |
|---------|-------|--------|
| 1 | Word pool / library mechanic | ✅ |
| 2 | Write screen — 5 states | ✅ |
| 3 | Feedback + correction design | ✅ |
| 4 | Progress + re-engagement logic | ✅ |
| 5 | Onboarding flow | 🔜 |
| 6 | Dashboard design | 🔜 |
| 7 | Quiz mechanic | 🔜 |
| 8 | Notifications strategy | 🔜 |
| 9 | Data structure + architecture | 🔜 |
