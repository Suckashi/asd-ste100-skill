# Sources: the eli5 / show-me family, consolidated

This skill is a merge, not an invention. Every rule in `SKILL.md` came from one of the variants below, or from a disagreement between two of them that this skill had to settle. Each entry says what the source does, what it does well, and what this skill took or left. Links were live in October 2026.

## The variants

### 1. `eli5` in anthropics/claude-plugins-community (Thariq Shihipar)

- **What it is.** A ten-line SKILL.md. The whole instruction: explain like I'm someone who knows nothing about this topic, using an HTML artifact with big pictures and few words. The topic comes from `$ARGUMENTS`.
- **What it does well.** It is one sentence, and it works. The model already knows how to explain. The skill only fixes the reader and the medium.
- **Known issue.** The frontmatter description says "like I'm a 5 year old". The body says "someone who knows nothing about this topic". Commentary on DEV (gridport) and on Andrew Ou's blog settles it as "zero background, not zero intelligence".
- **Taken.** The `new` default. The `html` form and its "big visuals, few words" rule. The restraint of a short prompt.
- **Left.** HTML for every answer. Three lines of prose are faster to read than a rendered page when three lines would do.

### 2. `eli5` in SJ-Wu/skills, and DreambigOu/ELI5 (near-identical)

- **What it is.** An audience-first explainer. It has a table for age (5, 10, 15, 20 to 30, 40+) and one for grade level (5th grade to graduate school). It has a table for job role (manager, engineer, designer, director, colleague, product manager) and one for relationship (partner, parents, kids, friend). Default audience is age 5.
- **Structure.** State the what, give an analogy, add detail to the right depth, end with the so-what. Tone matches the reader: enthusiastic for young children, light for teens, confident for professionals, warm for family.
- **Reminders.** Never talk down. For code, purpose before mechanism. Ruthless simplification is fine for non-technical readers. Length matches the reader.
- **Taken.** The audience axis and the mapping table (`references/audiences.md`). The four-part structure. All four reminders.
- **Changed.** The default is `new`, not age 5.

### 3. nathanksou/eli5 (a fork of Julius Brussee's Caveman)

- **What it is.** A voice, not a one-shot. Three levels. `easy` is for beginners: one analogy max, every term defined. `chill` is the default: plain language, analogies only when the idea is not obvious. `quick` is for advanced readers: no analogies, just the answer. The level is sticky until "normal mode" or "stop eli5". The stated goal is fewer output tokens.
- **Rules.** Cut filler words, pleasantries, hedging phrases, intro phrases, transitions, restatement. Lead with the answer. Fragments and symbols (`->`, `&`) allowed. Code blocks, commits, PR descriptions, and exact error messages stay unchanged.
- **Auto-clarity.** The voice pauses for security warnings, confirmations of irreversible actions, safety-critical ordered steps, and a repeated question.
- **Taken.** The level ladder (as `kid`, `new`, `peer`, plus `boss` from source 2). Sticky mode on "mode" phrasing. The clarity override. "Code artifacts stay normal."
- **Left.** Cutting hedging phrases. This repository's position, inherited from the STE skill, is that a hedge is a claim about confidence and must survive any rewrite. Also left: fragments and symbol shorthand, which cost a `new` reader more than they save.

### 4. `show-me` in SJ-Wu/skills

- **What it is.** Answer with a picture instead of prose. Eight forms: pseudocode, call tree, component tree, shallow file tree, Mermaid diagram, diff, full code block, and a single HTML file. The diff comes in four variants: component, file layout, call tree, and state or control flow. The core instruction: skip the preamble, keep prose brief, choose the smallest view that makes the key point clear.
- **Rules.** Put each visual next to the short text it supports. Include only the calls, files, props, states, or boundaries needed for this question. Use one form, several, or none. All of them is almost never right. Open an HTML file with `open`, `xdg-open`, or `start`.
- **Triggers.** "Show me", "draw it", "diagram this", "visualize this", "sketch it out", "what does the flow look like", "let me see the architecture". Also when a prose answer runs long, or when the user is choosing between options that differ mainly in shape.
- **Taken.** The entire form axis, the selection rule, the placement rule, the node-budget rule, the opener commands, and the trigger phrases.

### 5. `eli5` in dzhng/skills

- **What it is.** A spec and change explainer for a technical reader who has not seen the code or the session. Three required headings: `Problem`, `Solution`, `Schema changes`. One concrete scenario from trigger to outcome, today versus proposed. Define every term at first use. Do not rely on labels invented during the work. The text must stand alone without the diff. List every durable-contract change, and say explicitly when there is none. Call out destructive resets, migrations, compatibility behavior, eventual consistency, and intentional data loss. Marked `disable-model-invocation: true`, so it only runs when called.
- **Taken.** The `change` mode almost whole. The "define or avoid" rule. The "no session-invented labels" rule. "Simplify the telling, not the claims" is a paraphrase of its purpose statement.

### 6. haunguyendev/feynman-skill

- **What it is.** The Feynman technique as eight commands.
  - `map`: 5 to 8 questions worth asking, foundations first.
  - default explain, "like I'm 12": core idea, analogy, example, one-sentence summary, repeatable script.
  - `gaps`: you explain, it finds the weak point and gives 5 test questions.
  - `script`: messy notes into a read-aloud script.
  - `quiz`: Socratic, one question at a time, rising difficulty.
  - `analogy`: 5 analogies, where each breaks, the best one as a line.
  - `system`: a study plan with a deadline.
  - `break`: the hidden assumption and the untested edge case.

  It reads real source before explaining code. It answers in the user's language.
- **Taken.** The `map`, `analogies`, `quiz`, `gaps`, and `break` modes. "Say where the analogy breaks." "Read the real thing first." The user-language rule. The one-sentence takeaway.
- **Left.** `script` and `system`. They are study tools, not explanation.

### 7. alexanderop/walkthrough

- **What it is.** An interactive HTML page with a Mermaid flowchart or ER diagram of 5 to 12 concepts. Click a node for a plain-English description, file paths, and highlighted code. The agent explores the codebase with parallel subagents first, then synthesizes, then draws. Triggers on "walk me through", "explain this flow", "how does X work", "explain the tables".
- **Taken.** The 5 to 12 node cap. Explore before drawing. The walkthrough as one flavor of the `html` form. The "walk me through" trigger.

### 8. dogum/html-artifacts

- **What it is.** A gate and a standard for HTML output. HTML wins for comparisons, plans, code reviews, explainers, post-mortems, and dashboards. Markdown stays for short replies, code-only output, disposable summaries, and files that belong in git. Each page is one self-contained file, works in light and dark, meets an accessibility and print baseline, and gets rendered and screenshotted before delivery.
- **Taken.** The gating rule for `html`. The self-contained, two-theme, phone-readable requirements. The "render before you deliver" step in `references/forms.md`.

### 9. Mermaid skills (WH-2099/mermaid-skill, mgranberry/mermaid-diagram-skill, zabolotiny/mermaid-diagram-claude-code)

- **What they are.** Diagram generators covering 20+ Mermaid types. One has a render step that catches syntax errors. One auto-triggers on "explain the login flow" and "how does the API authentication work".
- **Taken.** The diagram-type selection table in `references/forms.md`. The "how does X work" trigger. The habit of checking that a diagram renders.

### 10. Commentary

Posts by gridport (DEV), Andrew Ou, bswen, papayota, and Mika Reyes on the official eli5 skill. They share three conclusions. The skill works because it fixes only the reader and the medium. "Five-year-old" should mean zero background rather than low intelligence. A page with big visuals beats a wall of text for a first encounter with a topic. Those three conclusions set this skill's defaults.

## Where the variants disagree, and what this skill decided

| Question | What the variants do | Decision here |
|---|---|---|
| A literal five-year-old, or an adult with no background? | The official eli5 says both. SJ-Wu defaults to age 5. Feynman says 12. | `new`: zero background, adult. `kid` only when the user asks for it. |
| Analogies always, sometimes, or never? | `easy` always, `quick` never, Feynman always with limits. | By level. Every analogy says where it breaks. |
| HTML for every answer, or never? | The official eli5 always. nathanksou never. html-artifacts gates it. | Gated. On request, or when the idea is spatial. |
| Cut hedges to save tokens? | nathanksou cuts hedging phrases. | No. Cut filler, keep hedges. A hedge is a claim. |
| Sticky voice or one-shot answer? | nathanksou sticky. The rest one-shot. | One-shot. Sticky when the user says "mode". |
| One command or many? | Feynman has eight. show-me and eli5 are separate skills. | One skill. Three option words. |
| Fixed structure or free? | DreambigOu has a fixed four-part structure. The official eli5 has none. | Fixed parts. Drop any part that adds nothing for this reader. |
| Who is the `change` reader? | dzhng assumes a technical reader. | Same, unless an audience word says otherwise. |

## Links

- https://github.com/anthropics/claude-plugins-community/blob/main/eli5/skills/eli5/SKILL.md
- https://github.com/SJ-Wu/skills (eli5, show-me)
- https://github.com/DreambigOu/ELI5
- https://github.com/nathanksou/eli5
- https://github.com/dzhng/skills/blob/main/skills/engineering/eli5/SKILL.md
- https://github.com/haunguyendev/feynman-skill
- https://github.com/alexanderop/walkthrough
- https://github.com/dogum/html-artifacts
- https://github.com/WH-2099/mermaid-skill
- https://github.com/mgranberry/mermaid-diagram-skill
- https://github.com/zabolotiny/mermaid-diagram-claude-code
- https://dev.to/gridport/claude-codes-eli5-skill-explained-how-it-works-setup-and-when-to-use-it-c6
- https://andrewou.pages.dev/posts/building-an-eli5-skill-for-claude/
- https://docs.bswen.com/blog/2026-08-25-claude-code-eli5-skill/
- https://en.papayota.com/blog/claude-eli5-skill
- https://mikareyes.com/ai/how-to-learn-anything-eli5-skill
