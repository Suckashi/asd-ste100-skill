# Sources: the eli5 / show-me family and the STE skill, consolidated

The `clarify` skill is a merge, not an invention. Every rule in `SKILL.md` came from one of three places. One of the variants below. The ASD-STE100 skill this repository started as. Or a disagreement between two of them that this skill had to settle. Each entry says what the source does, what it does well, and what this skill took or left. Links were live in October 2026.

## Popularity, October 2026

GitHub stars as the repo page showed them on 14 October 2026, rounded where GitHub rounds. Install counts are from the skills.sh registry as reported by third-party leaderboards, and only where one was found. Stars for a collection repo count the whole collection, not the one skill.

| Repo | Stars | Installs | Skill(s) used here | Absorbed? |
|---|---|---|---|---|
| JuliusBrussee/caveman | 111k | | terse, token-saving voice | Yes, as the `terse` reader. nathanksou/eli5 is a fork of it. |
| humanlayer/skills | 5.2k | ~12.5k for show-me | show-me | Yes, the whole form axis |
| danyuchn/asd-ste100-skill | 4.8k | | STE rewrite | Yes, the rewrite job and the agent reader |
| anthropics/claude-plugins-community | 4.6k | | eli5 (one of ~6 plugins) | Yes, the new default and the html form |
| DreambigOu/ELI5 | 1.7k | | eli5 | Yes, the reader axis and the explain structure |
| dzhng/skills | 1.3k | | eli5 (one of ~30 skills) | Yes, the change mode |
| WH-2099/mermaid-skill | 291 | | mermaid | Yes, the diagram-type table |
| careerhackeralex/visualize | 236 | | visualize | No. Overlaps the html form. |
| dogum/html-artifacts | 151 | | html-artifacts | Yes, the html gate |
| alexanderop/walkthrough | 144 | | walkthrough | Yes, the node cap and explore-first |
| mgranberry/mermaid-diagram-skill | 10 | | mermaid | Yes, the render check |
| zabolotiny/mermaid-diagram-claude-code | 7 | | mermaid | Yes, the "how does X work" trigger |
| nathanksou/eli5 | 6 | | eli5 | Yes, levels, sticky mode, clarity override |
| SJ-Wu/skills | 0 | | eli5, a copy of show-me | Yes, trigger phrases |
| haunguyendev/feynman-skill | 0 | | feynman | Two rules only. The five modes were removed. |

What the table says: the three things many people actually install are a terse voice (caveman), a visual answer (show-me), and an STE rewrite. The Feynman modes come from a repo nobody starred. The reader axis comes from a 1.7k-star repo. Weigh that when deciding what to keep in `SKILL.md`.

## The note that connects them

Andrej Karpathy, 2 October 2026, https://x.com/karpathy/status/2105819303471976479. The note says we will spend much more time trying to understand the outputs of language models, and lists four ways to make that easier, in rising order of effort:

1. Ask the model to explain in ASD-STE100 Simplified Technical English. Secondary sources and two derived skills report a softer variant, "80% of the way there". The original wording could not be checked from this environment, so treat the exact phrase as reported.
2. Ask for diagrams.
3. Ask for an interactive HTML page.
4. Ask for a 3Blue1Brown-style explainer video, with ElevenLabs narration.

That ladder is the reason the rewrite job and the explain job belong in one skill. Rung 1 is the rewrite job. Rung 2 is the `diagram` and `tree` forms. Rung 3 is the `html` form. Rung 4 is out of scope. "80% of the way" is the Flavored mode.

Two cautions. The image attached to the note is an ASD-STE100 cheat sheet, and reviewers found it inverts one dictionary rule and approves a verb the standard rejects. This skill does not use that sheet. Second, neither this repository nor the upstream danyuchn/asd-ste100-skill cited the note before 14 October 2026, so the link is reconstructed from timing and content, not from the upstream author's own statement.

Skills built directly on the note, all small as of 14 October 2026: prithivrajmu/asd-ste100 (11 stars, 80% mode by default, strict on request, diagram, HTML, video), ohernandezdev/karpathy-output (5 stars, picks the cheapest rung that makes a claim checkable, treats the artifact as a view and not as evidence), samirsawarkar/asd-ste100-skill (0 stars, a prompt compiler with tiers and a Manim video pipeline).

## The variants

### 1. `eli5` in anthropics/claude-plugins-community (Thariq Shihipar)

- **What it is.** A ten-line SKILL.md. The whole instruction: explain like I'm someone who knows nothing about this topic, using an HTML artifact with big pictures and few words. The topic comes from `$ARGUMENTS`.
- **What it does well.** It is one sentence, and it works. The model already knows how to explain. The skill only fixes the reader and the medium.
- **Known issue.** The frontmatter description says "like I'm a 5 year old". The body says "someone who knows nothing about this topic". Commentary on DEV (gridport) and on Andrew Ou's blog settles it as "zero background, not zero intelligence".
- **Taken.** The `new` default. The `html` form and its "big visuals, few words" rule. The restraint of a short prompt.
- **Left.** HTML for every answer. Three lines of prose are faster to read than a rendered page when three lines would do.

### 2. DreambigOu/ELI5, and the `eli5` in SJ-Wu/skills (near-identical)

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

### 4. `show-me` in humanlayer/skills (HumanLayer, Dex Horthy)

- **Where it lives.** `plugins/show-me/skills/show-me/SKILL.md` in humanlayer/skills. Install with `npx skills add humanlayer/skills --skill show-me`. The copy in SJ-Wu/skills reproduces it format for format, down to the example function names. That copy adds trigger phrases. Credit goes to HumanLayer.
- **What it is.** Answer with a picture instead of prose. Eight forms: pseudocode, call tree, component tree, shallow file tree, Mermaid diagram, diff, full code block, and a single HTML file. The diff comes in four variants: component, file layout, call tree, and state or control flow. The core instruction: skip the preamble, keep prose brief, choose the smallest view that makes the key point clear.
- **Rules.** Put each visual next to the short text it supports. Include only the calls, files, props, states, or boundaries needed for this question. Use one form, several, or none. All of them is almost never right. Open an HTML file with `open`, `xdg-open`, or `start`.
- **Triggers.** The HumanLayer original is marked `disable-model-invocation: true`, so it runs only when the user types `/show-me`. The SJ-Wu copy adds "show me", "draw it", "diagram this", "visualize this", "sketch it out", "what does the flow look like", and "let me see the architecture". It also adds two situations. A prose answer that runs long. A choice between options that differ mainly in shape.
- **Taken.** From HumanLayer: the entire form axis, the selection rule, the placement rule, the node-budget rule, and the opener commands. From the SJ-Wu copy: the trigger phrases, because this skill lets the model invoke it.

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
- **Taken.** "Say where the analogy breaks." "Read the real thing first." The user-language rule. The one-sentence takeaway. The `map`, `analogies`, `quiz`, `gaps`, and `break` modes were in `SKILL.md` at first. On 14 October 2026 they were removed, because they are study tools, not explanations, and the source repo has no stars.
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

### 10. asd-ste100 (this repository's original skill)

- **What it is.** A rewrite skill built on ASD-STE100, the controlled-language standard the aerospace and defense industry built so maintenance instructions cannot be misread. Two modes: Strict for text a program parses, STE-flavored for prose humans read. Ten structural rules, four lexical rules, a simple-tense rule with one exception for modality, a six-habit scan checklist, and a stdlib-only linter. Default output is the rewritten text alone, with a rule table on request.
- **What it does well.** It names the one thing every simplification skill gets wrong: a hedge is a claim, and shortening a sentence by promoting "may have failed" to "failed" changes what it says. It also separates the rules it can verify (sentence shape) from the rules that need ASD's dictionary (word choice), and says so instead of implying compliance.
- **Taken.** The whole `rewrite` job. The `agent` reader. The modality rule, promoted to a rule that holds in every job. The "never add a fact" rule. The structural sentence discipline (short, active, one idea, no semicolons) as the house style for every reader. The linter.
- **Changed.** The two STE modes are no longer chosen by text type. They follow from the reader: `agent` gives Strict, a human reader gives Flavored.

### 11. JuliusBrussee/caveman

- **What it is.** A terse voice for coding agents, 111k stars. Answer first, then reason, then next step. No greeting, preamble, recap, or closer. Short words. Drop articles when meaning survives. Never drop not, never, no, only, except. Keep numbers, units, code, commands, paths, and errors verbatim. One idea per sentence, with ASD-STE100 named as the floor. Plain prose returns for security warnings, irreversible actions, step order, a confused user, anything saved outside the chat, and questions to the user. Sticky until "stop caveman" or "normal mode". Two louder levels, `ultracave` and `megacave`.
- **What it does well.** It proves that most people who ask for "simpler" want "shorter", and that short and exact are not in conflict. Its pre-send check is the best one-paragraph editing rule in the family. Delete the opening sentence that announces intent. Delete the closing recap. Confirm every negation and number verbatim.
- **Taken.** The `terse` reader, added 14 October 2026: answer-reason-next-step order, dropped articles, protected negations, the no-performance rule, and the plain-prose exceptions. The sticky phrases. The pre-send check folded into "Lead with the answer".
- **Changed.** Caveman removes hedges. This skill drops hedging phrases about the speaker ("I think") but keeps the modal inside a claim ("may fail"), because the modal is content. `terse` is explain-only: on the rewrite job it becomes `peer`, because STE forbids dropped articles.

### 12. Commentary

Posts by gridport (DEV), Andrew Ou, bswen, papayota, and Mika Reyes on the official eli5 skill. They share three conclusions. The skill works because it fixes only the reader and the medium. "Five-year-old" should mean zero background rather than low intelligence. A page with big visuals beats a wall of text for a first encounter with a topic. Those three conclusions set this skill's defaults.

## Where the variants disagree, and what this skill decided

| Question | What the variants do | Decision here |
|---|---|---|
| A literal five-year-old, or an adult with no background? | The official eli5 says both. SJ-Wu defaults to age 5. Feynman says 12. | `new`: zero background, adult. `kid` only when the user asks for it. |
| Analogies always, sometimes, or never? | `easy` always, `quick` never, Feynman always with limits. | By level. Every analogy says where it breaks. |
| HTML for every answer, or never? | The official eli5 always. nathanksou never. html-artifacts gates it. | Gated. On request, or when the idea is spatial. |
| Cut hedges to save tokens? | nathanksou and caveman cut hedging phrases. | Cut filler and self-hedges ("I think"). Keep the modal in a claim ("may fail"). A hedge about the world is content. |
| Sticky voice or one-shot answer? | nathanksou sticky. The rest one-shot. | One-shot. Sticky when the user says "mode". |
| One command or many? | Feynman has eight. show-me and eli5 are separate skills. | One skill. Three option words. |
| Fixed structure or free? | DreambigOu has a fixed four-part structure. The official eli5 has none. | Fixed parts. Drop any part that adds nothing for this reader. |
| Who is the `change` reader? | dzhng assumes a technical reader. | Same, unless an audience word says otherwise. |
| Explain and rewrite: one skill or two? | Every variant does one or the other. The STE skill only rewrites. | One skill. The job is inferred from whether the input is a question or a text. |
| Which rules does a machine reader get? | The explain family has no machine reader. STE picks a mode by text type. | A fifth reader, `agent`, that switches on the full Strict rule set. |

## Links

- https://x.com/karpathy/status/2105819303471976479 (the note that connects the family)
- https://github.com/prithivrajmu/asd-ste100
- https://github.com/ohernandezdev/karpathy-output
- https://github.com/samirsawarkar/asd-ste100-skill
- https://github.com/danyuchn/asd-ste100-skill (the STE skill this repository forked from)
- https://www.asd-ste100.org/
- https://github.com/anthropics/claude-plugins-community/blob/main/eli5/skills/eli5/SKILL.md
- https://github.com/humanlayer/skills (show-me, in plugins/show-me/skills/show-me/SKILL.md)
- https://github.com/SJ-Wu/skills (eli5, and a copy of show-me)
- https://github.com/JuliusBrussee/caveman
- https://github.com/careerhackeralex/visualize
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
