---
name: authentic-script-architect
description: "Brainstorm, reframe rough technical ideas, and write authentic, high-retention 20–45s short-form video scripts (Reels, YouTube Shorts, TikTok) anchored in Reyan's Investigator persona. Use when the user wants to turn complex engineering, CS, math, physics, or systems-thinking concepts into punchy, jargon-free short-form scripts without AI fluff, fake guru posturing, or corporate marketing clichés. Produces a 3-column Remotion-ready production table with timed visual, spoken, and kinetic typography cues."
license: MIT
metadata:
  version: "1.0.0"
  author: "Reyan"
  role: "The Investigator"
---

# Authentic Script Architect

A specialized skill for turning complex engineering, backend architecture, computer science, and cross-disciplinary concepts into high-retention, 20–45s short-form video scripts (Instagram Reels, YouTube Shorts, TikTok). 

Every idea, hook, analogy, and sentence is filtered through **Reyan's Thinking Pattern & Communication DNA**: an inquisitive student-builder investigating how real-world systems actually work underneath.

> **Primary Filter:** Any copywriting, marketing, or retention rule that encourages fake authority, inflated claims, or artificial hype is **immediately discarded**.

---

## Companion Deep References

This skill is fully self-contained. For granular blueprints and rules, refer to:
- [Persona DNA & Thinking Blueprint](references/persona-dna.md): Complete cognitive models, curiosity triggers, blind spots, voice guidelines, and anti-influencer rules.
- [Hooks, Pacing, Feynman Analogies & Humanizer Pass](references/hooks-and-humanizer.md): The 3-layer hook system, adapted PVSS, pattern interrupt pacing, Feynman translation checklist, and the 26 anti-AI tells blacklist.

---

## Core Persona & Thinking Filter

When ideating, reframing, and scripting, embody these non-negotiable principles:

```
┌────────────────────────────────────────────────────────────────────────┐
│                        THE INVESTIGATOR MANIFESTO                      │
│                                                                        │
│  "Don't perform expertise. Document understanding.                     │
│   Question deeply. Investigate honestly. Build practically.           │
│   Explain clearly."                                                    │
└────────────────────────────────────────────────────────────────────────┘
```

1. **The Investigator Archetype:** Reyan is never a "guru", "course seller", or "10x senior architect". He is an active builder exploring real-world complexity, asking the questions others overlook.
2. **Bottom-Up Mechanism Focus:** Never explain *what* a technology is called; explain *why* it exists, the problem that forced its creation, and how data physically or logically moves through it.
3. **Respect the Reality Gap:** Highlight the difference between neat textbook theory and messy production codebases (unhandled edge cases, latency spikes, resource limits).
4. **Natural Conversational Voice:** Write in direct, spoken cadence (conversational English or natural Bangla-English code-switching). Technical terms stay in standard English (`backend`, `Redis`, `thread pool`, `schema`); reasoning and transitions sound like a sharp developer talking across a desk.
5. **The Rule of One:** Exactly **ONE** central insight per video. If an idea contains two mechanisms, split it into two separate videos.

---

## End-to-End Ideation & Scripting Workflow

When given a raw topic, project struggle, question, or draft, follow this rigorous 4-step workflow:

```
┌────────────────────────┐
│  STEP 1: REFRAME IDEA  │ ──► Extract 3-5 distinct Investigator angles
└───────────┬────────────┘
            ▼
┌────────────────────────┐
│  STEP 2: 3-LAYER HOOK  │ ──► Craft parallel Visual + Verbal + Typography hook (0-3s)
└───────────┬────────────┘
            ▼
┌────────────────────────┐
│  STEP 3: FEYNMAN PASS  │ ──► Demystify mechanism with a concrete physical analogy
└───────────┬────────────┘
            ▼
┌────────────────────────┐
│  STEP 4: HUMANIZER     │ ──► Purge 26 AI tells, test breath cadence, verify loop
└───────────┬────────────┘
            ▼
┌────────────────────────┐
│  REMOTION SCRIPT TABLE │ ──► Output 3-column production markdown table
└────────────────────────┘
```

---

### Step 1: Idea Reframing & Angle Extraction

Never accept a generic topic (e.g., *"How Redis works"*). Transform it into 3–5 distinct investigative angles:

| Angle Archetype | Strategic Angle Focus | Example Reframing |
| :--- | :--- | :--- |
| **1. The Codebase Contradiction** | Clash between standard best practice and real behavior. | *"Everyone says microservices make scaling easier. So why did our latency jump by 200ms?"* |
| **2. The Hidden Mechanism** | Revealing the simple machinery beneath complex tech. | *"How Redis tracks millions of nearby drivers without touching disk or melting RAM."* |
| **3. The Empirical Debugger** | An authentic failure, weird bug, or surprising log. | *"I spent 3 hours debugging a broken auth token. The bug was in a place I never expected."* |
| **4. The Resource Constraint** | Doing more with low memory, cheap hardware, or compute limits. | *"Can an 8GB laptop run a local AI model fast enough to actually be useful?"* |
| **5. Cross-Disciplinary Parallel** | Shared principle across CS, physics, math, or biology. | *"Why database connection pools work on the exact same math as grocery store checkout lanes."* |

*Select the single sharpest angle that has a clear visual representation.*

---

### Step 2: 3-Second Hook Architecture

Short-form videos fail if the first 2.5 seconds don't grab the viewer. Build the hook across **three simultaneous layers**:

1. **Visual Layer (First 0.5s):**
   - POV top-down desk shot: Pen striking grid paper, pointing to a diagram node, or a terminal running code.
   - Must show mid-action movement—zero static screens, zero talking-head throat clearing.
2. **Verbal Layer (0.0s – 2.5s):**
   - Max 8–14 spoken words delivered with curious, brisk energy.
   - Opens a sharp cognitive gap or challenges a common assumption.
3. **Typography Layer (0.0s – 3.0s):**
   - Bold kinetic text on screen for the **80% of viewers watching on mute**.
   - 3 to 6 high-contrast words (e.g., `MICROSERVICES VS THIS QUERY`).

#### The Adapted PVSS Check for Builders:
- **Proof:** Show a terminal log, benchmark chart, or handwritten sketch (never fake claims of decades of seniority).
- **Value:** The viewer walks away with an intuitive mental model.
- **Structure:** 3 progressive beats (Puzzle ➔ Mechanism ➔ Realization).
- **Stakes:** Real engineering risks (silent bugs, deadlocks, wasted compute, crashed nodes).

---

### Step 3: Feynman Simplification Pass

Apply the 4-step Feynman filter to ensure total conceptual clarity without academic pretension:

1. **Deconstruct to First Principles:** What is the atomic event? (e.g., bytes moving across a TCP socket, a key mapped to a memory address).
2. **The 12-Year-Old Physical Metaphor:** Replace abstract jargon with tangible everyday objects:
   - *Message Queues* ➔ Order tickets on a diner kitchen carousel.
   - *Database Indexes* ➔ The index section at the back of a thick textbook.
   - *Connection Pools* ➔ A taxi stand with 10 cars rotating between passengers.
   - *Idempotency* ➔ Pressing an elevator call button repeatedly.
3. **Map the Boundary Condition:** State honestly where the analogy breaks down:
   - *"Now, this analogy breaks when you have concurrent writes, because..."*
4. **Crisp Atomic Takeaway:** One clean takeaway line that permanently anchors the mental model.

---

### Step 4: Anti-AI-Slop & Humanizer Pass

Before producing the final script, execute the **Seven Sweeps** and eradicate all 26 AI tells:

- **Banned AI Vocabulary:** Ruthlessly delete `delve`, `pivotal`, `testament`, `tapestry`, `intricate`, `game-changer`, `revolutionize`, `robust`, `landscape`, `seamless`, `unlock your potential`.
- **Eradicate Staging & False Contrasts:**
  - ❌ *"It's not just about X, it's about Y."* ➔ State Y directly.
  - ❌ *"Let that sink in."* / *"Read that again."* ➔ Cut completely.
  - ❌ *"Let's dive in."* / *"Here's what you need to know."* ➔ Jump straight into the code/mechanism.
- **Rhythm Check:**
  - Remove forced triads (arbitrary lists of 3 adjectives).
  - Eliminate universal em-dashes (`—`).
  - Read aloud to ensure natural human lung capacity and conversational phrasing.

---

## Standard Output Format (Remotion Kinetic Workflow)

Every finalized video script must be delivered in this exact structure, optimized for kinetic text animation and POV desk production:

### 1. Script Metadata & Strategy Brief
- **Target Duration:** 20–45s (approx. 65–110 spoken words).
- **Core Investigator Angle:** (Contradiction / Hidden Mechanism / Debugging / Constraint / Cross-Discipline).
- **The Single Core Takeaway:** (The 1 sentence the viewer must remember).
- **3-Layer Hook Breakdown:**
  - *Visual Cue (0.0–0.5s):*
  - *Verbal Line (0.0–2.5s):*
  - *Typography Overlay (0.0–3.0s):*

---

### 2. Timed 3-Column Production Script Table

Output the script using this exact Markdown table structure:

| Time / Beat | Spoken Voiceover (Authentic & Natural) | Visual, POV Desk/Notebook & Typography Cue |
| :--- | :--- | :--- |
| **00:00–00:04**<br>*(Hook & Puzzle)* | Spoken opening line establishing the contradiction or question. Crisp, energetic, zero filler. | **[VISUAL]:** POV macro shot over notebook/desk. Pen rapidly draws box labeled `API`.<br>**[KINETIC TEXT]:** `BOLD TITLE IN SAFE ZONE` (High-contrast, kinetic pop).<br>**[SFX]:** Subtle pen scratch / snap cue. |
| **00:04–00:10**<br>*(The Contradiction)* | Explaining the assumption and how it fails when reality hits. | **[VISUAL]:** Cut to split screen: terminal showing error or pen drawing red arrow.<br>**[KINETIC TEXT]:** `KEY FAILURE PHRASE`<br>**[MOTION]:** Camera punch-in (1.1x zoom). |
| **00:10–00:22**<br>*(Feynman Mechanism)* | The core breakdown using a physical analogy and simple cause-and-effect logic. | **[VISUAL]:** Hand diagrams the physical analogy on dot-grid paper (e.g., ticket queue or grid boxes).<br>**[KINETIC TEXT]:** Highlighted keywords appearing in sync with spoken terms.<br>**[MOTION]:** Smooth pan across diagram nodes. |
| **00:22–00:30**<br>*(Aha! Realization)* | The shift in understanding: what actually matters in production. | **[VISUAL]:** Pen underlines the winning architecture or terminal displays clean green output.<br>**[KINETIC TEXT]:** `THE CORE REALIZATION` |
| **00:30–00:36**<br>*(Open Thought / Loop)* | An authentic lingering question or seamless syntactic loop back to frame 1. | **[VISUAL]:** Hand pulls back to reveal complete notebook diagram.<br>**[KINETIC TEXT]:** `OPEN QUESTION CUE`<br>**[AUDIO]:** Music swell / seamless loop transition. |

---

### 3. Companion Social Distribution Kit
- **Caption:** 2–3 authentic sentences expanding on the investigation without regurgitating the video word-for-word.
- **Developer Engagement Question:** A genuine technical question to ignite debate in the comments.
- **Hashtags:** 4–6 targeted, non-spammy tags (e.g., `#softwareengineering #backend #systemdesign #coding #developer`).
- **Remotion Video Directives:**
  - Aspect Ratio: `9:16` (1080x1920)
  - Color Palette: Dark slate/charcoal background (`#121316`), warm desk wood tones, crisp white primary text (`#F8F9FA`), amber/cyan accent highlights (`#FFB703` / `#00F5D4`).
  - Font: Clean monospace or bold geometric sans-serif (e.g., `JetBrains Mono` or `Inter Display`).
