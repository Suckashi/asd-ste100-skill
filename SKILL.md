---
name: clarify
description: "Make any text or idea clear for one chosen reader. Explain a topic, code, error, diff, or decision for a kid, a newcomer, a peer, a boss, or someone who wants the fewest words. Shapes: prose, pseudocode, a tree, a Mermaid diagram, a diff, code, or one HTML page. Or rewrite dense English or Chinese so a person or a machine cannot misread it. English gets ASD-STE100 Simplified Technical English, Chinese gets the same discipline. Full STE for an agent reader (tool descriptions, error messages, system prompts), the 80% version for a person. Triggers: explain, eli5, explain like I'm 5, explain this to my <someone>, dumb it down. Also: show me, draw it, diagram this, walk me through, how does X work, tldr, be brief, caveman mode. Also: disambiguate, STE100, Simplified Technical English, plain-language rewrite, rewrite so an agent cannot misread this, 改寫, 簡化這段, 消除歧義. Partial matches count. Asks only when the request is unclear. Not for creative or marketing copy."
version: 1.0.0
---

# Clarify

Every eli5, show-me, explain-to-my-boss, caveman, walkthrough, and plain-language rewrite skill does one thing. It takes something and makes it clear to one specific reader. The skills differ on four choices only. Who the reader is. Whether the input is a question to answer or a text to transform. What shape the answer takes. How deep it goes. This skill names those four choices, gives each a default, and lets the user set any of them with one word.

It absorbs two families. The explain family (eli5, show-me, caveman, walkthrough, and their variants, listed in `references/sources.md`) became the **explain** job. The ASD-STE100 rewrite skill this repository started as became the **rewrite** job and the `agent` reader. STE was written for people first: aircraft technicians reading English as a second language. This skill keeps that use. A human reader gets the softer mode. A machine reader gets the full one.

## Invocation

```
/clarify [reader] [job] [form] [mode] <topic, question, text, file, error, or diff>
```

When the request is not clear enough to act on, Step 0 asks about the open choice and nothing else. Every option word is optional, in any order, and any of them settles that choice. Natural language maps onto the same words.

| Choice | Words | Default |
|---|---|---|
| Reader | `kid`, `new`, `peer`, `boss`, `terse`, `agent` | `new` for a question. `agent` for text a program will parse. `peer` for other text. |
| Job | `explain`, `rewrite` | Inferred from the input (Step 1) |
| Form (explain only) | `prose`, `pseudo`, `tree`, `diagram`, `diff`, `code`, `html` | `auto` |
| Mode (explain only) | `explain`, `change` | `explain` |
| Output (rewrite only) | `text`, `table` | `text` |

Phrase mapping. "eli5", "like I'm 5", "dumb it down", "I know nothing about this" set `new`. "My kid", "a child", "my daughter", or an age under 12 set `kid`. "Explain to my manager", "tell my boss" set `boss`. "For an agent", "so a model cannot misread this", "tool description", "STE100" set `agent`. "80% STE" sets rewrite with a human reader. "Show me", "draw it", "diagram this", "what does the flow look like" set form `auto` with a visual preference. "Walk me through" sets `tree` or `diagram`. "tldr" (in any spelling), "short version", "just the answer", "be brief", "less tokens", "caveman" set `terse`. "Show the diff", "which rules did it break", "before/after" set output `table`.

## Step 0: Guide the user when the request is not clear

Before you answer, check whether the input tells you three things. What the user wants done (the job). Who reads the answer (the reader). What the subject is (the topic). If one reading is clearly the most likely, take it and answer. Do not ask about a choice whose default would serve. Ask only when two readings would give materially different answers, and ask only about that choice. Offer options, not an open question.

| What is unclear | Signs | Ask |
|---|---|---|
| Everything | a bare `/clarify` | all three questions, then "Paste the text or ask your question." |
| The topic | an option word with nothing to apply it to (`/clarify boss`) | "What should I clarify? Paste the text or ask your question." |
| The job | pasted prose with no verb and no question: a tool description, a paragraph, a prompt | "Explain it, or rewrite it?" |
| The reader | "explain this to someone", "make this simpler", "for my team" when the team could be engineers or not | "For whom?" |
| Agent or human | "rewrite this", "clean this up" with no hint whether a program or a person reads the result | "Who reads the result: an agent, or a person?" |
| The shape | "make it visual", "draw something" without saying what, or a subject big enough that a tree, a diagram, and a page would differ a lot | "What shape?" |

Clear enough, so answer at once:

- A question: "how does X work", "what is X", "why does this fail". Explain, `new`, auto.
- Pasted program output: a stack trace, a log line, an error with a file and line number. Nobody rewrites a stack trace. Explain it.
- A trigger phrase: "eli5", "show me", "walk me through", "explain to my boss", "rewrite for an agent", "disambiguate", "apply STE".
- Any option word from the Invocation section.
- A change-shaped input: "explain this PR", a diff plus "explain".
- Picks the user already made earlier in the session.

How to ask. In Claude Code, call the AskUserQuestion tool once, with only the unresolved questions (one to three), the default option first and marked "(Recommended)". Anywhere else, print only the relevant lines of the text menu and wait.

| Question | Options | Default |
|---|---|---|
| What do you want? | explain it / explain a change (PR, diff, spec) / rewrite it for an agent (STE Strict) / rewrite it for people (STE Flavored) | explain it |
| For whom? (ignored for an agent rewrite) | someone new to the topic / a peer in the field / a boss or decision maker / a kid | someone new |
| What shape? (explain only) | auto, the smallest that works / a diagram or tree / pseudocode, code, or diff / one HTML page | auto |

```
What do you want?  1 explain (default)   2 explain a change   3 rewrite for an agent        4 rewrite for people
For whom?          a new (default)       b peer               c boss                        d kid
What shape?        x auto (default)      y diagram or tree    z pseudocode, code, or diff   w one HTML page
Reply with your picks and the topic, like `1 c y how does login work`. Enter alone takes the defaults.
```

Rules for the guide:

- One round, never two. If the reply is still unclear, take the defaults and answer. Say in one line which reading you took.
- `terse` and `table` are not on the menu. The user types them as words, or picks "Other" and names one.
- Picks stick for the session. A later unclear request reuses them. Say so in three words ("Same picks again."). The user says "menu" to choose again.
- The guide is the one place this skill asks before it answers. Once the user has picked, answer with no further preamble.

## Step 1: Pick the job

**Explain** when the user wants to understand something. That is a question, a topic, a piece of code, an error they hit, or a diff they have to review. The input is the subject. The output is new text about it.

**Rewrite** when the user hands over text and wants the same content in a clearer form. The asks sound like "rewrite this", "disambiguate this", "apply STE", "simplify this error message", or "clean up this tool description". The input is the material. The output must say exactly what the input said, and nothing more.

"eli5 this error" is explain. "Rewrite this error so an agent cannot misread it" is rewrite. When both readings are possible, explain. A missed rewrite costs one follow-up. A missed question loses what the user wanted to know.

## Step 2: Pick the reader

Decide who reads the answer before you write a word. The reader sets vocabulary, analogy use, length, and for `agent`, the whole rule set.

| Reader | Assume | Vocabulary | Analogies | Length |
|---|---|---|---|---|
| `kid` | No background and a short attention span | Everyday words only. No jargon at all. | One, carried through the whole answer | 3 to 6 sentences, under 80 words |
| `new` | No background in this topic, full adult intelligence | Plain words. Define each technical term at first use. | One, when the idea is not obvious | Under 250 words. One screen. |
| `peer` | Works in this field | Exact terms. Trade-offs and edge cases matter. | None, unless asked | As long as the question needs |
| `boss` | Decides and pays, does not build | Impact, risk, cost, options. Numbers where they exist. | Only if it shortens the answer | Under 120 words. The first sentence is the decision or the impact. No feature lists. |
| `terse` | An expert who wants the fewest words (the caveman voice) | Short words, standard acronyms, no invented abbreviations. Articles may go. | None | Minimum. Answer, reason, next step. |
| `agent` | A model, a tool, a translation pipeline, or a non-native reader, with no human to ask | The plainest word, used the same way every time | None | As long as it takes to be unambiguous, and not one word longer |

The word budgets are caps, not targets. Go over one only when the question has that many moving parts. Then say in one line that the answer runs long. A cap beats completeness: a `boss` answer that covers every rule has failed, however accurate it is.

Rules for picking the reader:

- **Default to `new` for a question.** The best-known eli5 skill says "like a 5 year old" in its title. Its body says "someone who knows nothing about this topic". The body is right. Assume zero background, not zero intelligence. Use `kid` only when the user names a child, an age under 12, or asks for the literal five-year-old version.
- **Default to `agent` for text a program will parse.** That means tool and function descriptions, error strings, inter-agent instructions, system prompts, and status reports. It also means any output another model reads with no human in the loop. A technician on a tarmac and a model parsing a tool description are in the same position: neither can ask what you meant.
- Map named readers to the nearest level, then adjust tone. "My mom", "my partner": `new` with a warm tone and analogies from daily life. "A grad student": `new` or `peer`, depending on whether their field is this field. "A designer", "a PM", "a director": `boss`, framed around what that role decides. The full table is in `references/audiences.md`.
- **Never talk down.** A simple explanation respects the reader. A cute one does not. No baby talk, no "imagine a magical box", no exclamation marks the content did not earn.
- **`terse` is a voice, not a person.** Answer first, then the reason, then the next step. No greeting, no preamble, no recap, no closer. Drop articles when the meaning survives. Never drop not, never, no, only, or except. Keep numbers and units exact. Drop hedging phrases about yourself ("I think", "it seems"), but keep the modal inside a claim: "may fail" stays "may fail". It is one word, and it is content. If the terse phrasing is not shorter, use plain phrasing. No performance: no "Caveman:" prefix, no "me think", no decorative emoji. Questions and option lists to the user, and anything that will be saved outside the chat, get plain prose. On the rewrite job `terse` is not a valid reader, because STE forbids dropped articles. Treat it as `peer` there.
- **Sticky mode.** The user can say "eli5 mode", "simple mode", "terse mode", "caveman mode", or "keep explaining like this". Then keep the reader for the rest of the session. Drop it when they say "normal mode", "stop eli5", or "stop caveman". Confirm the change in two words. Otherwise each call is one-shot.

## Step 3: Explain

### Shape

Pick the smallest view that makes the key point clear. Most answers need one visual or none. Two is sometimes right. All of them is never right.

| The question is about | Use | Example |
|---|---|---|
| An idea, a definition, a why | `prose` | "What is idempotency?" |
| Logic, an algorithm, a branching rule | `pseudo` | "What does this save handler do?" |
| What calls what, at runtime | `tree` (call tree) | "What happens when I submit the form?" |
| UI structure, state ownership, module boundaries | `tree` (component tree) | "Where does the modal state live?" |
| Which file owns what, or a wide refactor | `tree` (file tree) | "How is this package laid out?" |
| How parts talk to each other over time | `diagram` (Mermaid sequence) | "How does login work end to end?" |
| A flow with decisions, or a state machine | `diagram` (Mermaid flowchart or state) | "When does a job get retried?" |
| What changes, when the surrounding shape already exists | `diff` | "What will this refactor look like?" |
| Mostly new code, or a copyable target shape | `code` | "Show me the final function." |
| A spatial layout, a before/after screen, or an idea too dense for Mermaid | `html` | "Explain how a B-tree splits." |

Rules for every visual:

- Put the visual next to the two or three lines of text it supports. Text first, then visual, then the one line that says what to notice. That is the whole answer when the form is a tree, a diagram, a diff, or code: no analogy, no six-part structure, no example run. If the user wants the prose too, they ask.
- Include only the calls, files, props, states, or boundaries that answer this question. Cut the rest, even if it is real.
- Use the user's real names: real function names, real file paths, real field names. Never placeholder names when the real thing is in front of you.
- Diagrams hold 5 to 12 nodes. More than that is two diagrams or a tree.
- An `html` page is one self-contained file: big visuals, few words, real labels, readable on a phone, works in light and dark. Write it to the current project (or a scratch directory if the project must stay clean), then open it with `open` (macOS), `xdg-open` (Linux), or `start` (Windows). If no opener is available, print the path. Build `html` only when the user asks for it or when the idea is genuinely spatial. A rendered page is slower to read than three lines of prose when three lines would do.

Templates and a worked example for each form are in `references/forms.md`.

### Depth

**`explain` (default).** Build the answer from these parts, in this order. Drop any part that would add no information for this reader.

1. **What.** One sentence. The thing, in the reader's words.
2. **Analogy.** Something the reader already knows that behaves the same way. Say where the analogy breaks. An analogy with no stated limit teaches a wrong model.
3. **How.** The mechanism, at the depth this reader needs. For code, purpose before mechanism: what it is for, then how it does it.
4. **Example.** One concrete scenario, from trigger to outcome, with real values.
5. **So what.** Why this reader should care. What they can now do, decide, or stop worrying about.
6. **Takeaway.** One sentence the reader can repeat to someone else.

The six parts are a checklist, not headings. Do not label them in the output (no "Analogy:", no "Takeaway:", no "Why it matters"). The reader sees prose.

Cuts by reader. `peer` gets 1, 3, 4. `boss` gets 1, 5, 4, with 5 before 4, and nothing else. `kid` gets 1, 2, 6. `terse` gets 1 and 3, in fragments, plus the next step. `new` gets all six, inside the word budget.

**`change`.** For a diff, a PR, a spec, or a proposed design, read by someone who has not seen the code or the session. Use these headings, in this order:

1. **Problem.** What goes wrong today, in terms of what a user or operator sees. Why the current design causes it.
2. **Solution.** The new behavior as a before/after flow. Introduce each component by what it is responsible for, not by its filename.
3. **What changes.** Every schema, API, config, or other durable contract that the change adds, changes, removes, or resets. Say "none" explicitly when there is none. Call out migrations, destructive resets, and compatibility behavior even when users will never see them.
4. **What people will notice.** One short sentence.

Separate what exists today from what is only proposed. The text must stand on its own without the diff.

## Step 4: Rewrite

The rewrite job applies ASD-STE100, the controlled-language standard the aerospace and defense industry built so that maintenance instructions cannot be misread. STE removes the two biggest sources of misreading: words with more than one meaning, and sentences with more than one possible structure. `references/writing-rules.md` has the full rule summary and citations.

### Two modes, set by the reader

**Strict**, for reader `agent`: apply every rule below, including the length caps and the one-word-one-meaning discipline. Anywhere a wrong reading has a cost.

**Flavored**, for a human reader (`new`, `peer`, `boss`): apply the structural rules in full and treat the lexical rules as advisory. This is what Karpathy's note calls "80% of the way to ASD-STE100", and "80% STE" selects it. Keep the length caps, active voice, simple tenses, no phrasal verbs, no semicolons, no nominalization, no marketing adjectives. Drop the one-word-one-meaning lockdown. Prose needs some range, and a strict rewrite of prose reads as a personality transplant rather than a clarification. Then apply the reader's vocabulary rule from Step 2: define terms for `new`, keep them for `peer`, lead with impact for `boss`.

The split between structural and lexical rules is the split between what this skill can verify and what needs ASD's dictionary. STE's ~900-word approved dictionary is free to obtain but not free to redistribute. Issue 9, page 2, grants reproduction rights to eight listed categories of organisation. This project is in none of them. So this skill applies the dictionary's *principle*, the plainest word used the same way every time, and never claims dictionary compliance. For certified STE documentation, request the standard from the [official downloads page](https://www.asd-ste100.org/STE_downloads.html) and check word by word.

### Chinese text

The rewrite job also takes Chinese. STE has no Chinese edition, so apply the structural rules as mapped in `references/chinese.md`. Read that file with the Read tool before you write a word of a Chinese rewrite. The short version below is not enough on its own. The short version. The actor is the subject (avoid 被, 由). One instruction per sentence (no 並且, 然後 chains). 30 characters for an instruction, 40 for a description. No 「；」. Every sentence keeps its subject. At most two 的 in a chain. A numbered list for three or more steps. The lexical rules become two habits. One word for one action in this document (pick one of 確認, 檢查, 驗證). The verb, not 進行 or 加以 plus a noun. Keep every hedge: 可能, 也許, 應該, 約 stay. 應該 means obligation or inference. Resolve it to 必須 or 可能 only when the context makes the reading certain. Otherwise keep it and flag it. Output in the same script as the input, with Taiwan terms for Traditional Chinese. The output labels are Chinese too: `Kept as-is:` is 「保留原文：」, `Added definition:` is 「補充定義：」, and the rule table uses the headings 違反的規則, 原文, 改寫. The linter does not check Chinese.

### Structural rules, apply with confidence

| Rule | Do | Don't |
|---|---|---|
| Active voice | "The agent deletes the file." | "The file is deleted (by the agent)." Unless the actor is genuinely unknown or irrelevant. |
| No phrasal verbs (Rule 9.3) | "Remove the panel." / "Start the job." | "Take off the panel." / "Spin up the job." A two-word verb has meanings the parts do not predict. |
| One instruction per sentence | "Open the file. Read line 3." | "Open the file and read line 3, then check if it matches." |
| Sentence length | ≤20 words for instructions, ≤25 for descriptions | Long compound or subordinate-clause sentences |
| No semicolons (Rule 8.1) | Split into separate sentences | Any semicolon at all. STE bans the mark outright. Every other standard mark stays permitted, including the em dash, though a dash often signals a sentence that should be split. |
| Noun clusters | ≤3 words stacked as a noun phrase ("fuel pump valve") | 4+ word stacks ("high pressure fuel pump inlet valve assembly") |
| No ellipsis | Keep subject, verb, and article explicit even if it reads longer | Drop words to save space ("Files not backed up will be lost": which files?) |
| Keep modality | "The request **may have** failed." stays "may have" | Promote a hedge to a fact ("The request failed.") or invent a certainty the source did not state |
| Paragraph limits | One topic per paragraph, ≤6 sentences | Multi-topic paragraphs |
| Lists for sequences | A numbered or bulleted list for 3+ steps or conditions | A sequence buried in one prose sentence |

### Lexical rules, direction of travel only

| Rule | Do | Don't | Why it is weaker here |
|---|---|---|---|
| One word, one meaning | Pick one verb for one action and reuse it (always "check", never "check" / "verify" / "confirm" for the same action) | Rotate synonyms for the same idea | Consistency within a document is checkable. Which word is the *approved* one is not, without the dictionary. |
| One part of speech per word | "Apply oil to the valve" (oil as noun) | "Oil the valve" (oil as verb) | Whether "oil" is approved as a noun only is a dictionary fact. Prefer the noun form when both read equally well. |
| Verb, not noun (Rule 3.7) | "Analyze the log." | "Perform an analysis of the log." | Preferring the verb is safe anywhere. Knowing which verb is approved needs the dictionary. |
| Domain terms | Keep necessary technical nouns and verbs, defined once if not common English | Undefined jargon | STE allows a project glossary beyond its base dictionary. The base dictionary is absent here. |

### Simple tenses, with one exception

STE permits infinitive, imperative, simple present, simple past, simple future, and past participle as adjective. It excludes present perfect and other compound forms: "we received the report", not "we have received the report". Aircraft manuals never need present perfect. Status text sometimes does. "The job has completed" (and its output is available now) and "the job completed" (at some past point) are different statements. **Sometimes the compound form carries information the simple form cannot: current relevance, or a hedge as in "may have failed". Then keep it and flag the departure.** Elsewhere, follow the rule.

### Scan checklist

Six mechanical habits cover most of what makes machine-written English hard to parse. You can point at the exact word or mark that breaks each one. Scan for all six before you rewrite anything.

1. **Synonym rotation**: "the user", "the customer", "the client" for one thing. Pick one name, use it every time.
2. **Hedge stacking**: "it is important to note that this may potentially help to improve". State the claim, or delete it. Do not delete the hedge itself.
3. **Nominalization**: "perform an analysis of", "provides assistance to". Use the verb.
4. **Marketing adjectives**: seamless, robust, powerful, cutting-edge, effortless. Delete, or replace with the measurement that earns the claim.
5. **Run-on sentences**: several ideas joined by semicolons or dashes. One idea per sentence.
6. **Soft phrasal verbs**: spin up, reach out, dive into, kick off. Use the single plain verb.

### Process

1. Pick the mode from the reader. Keep the choice internal unless the user asked for the rule table.
2. Read the input once for meaning. Know what it must still say afterward before you change anything.
3. Walk it sentence by sentence. Flag every rule violation and every checklist habit. In Flavored mode, flag the lexical rules but do not enforce them. For a mechanical first pass, run `scripts/ste-lint.py` (stdin or file args, `--json` for structured output). It checks semicolons, sentence length, phrasal verbs, nominalization, marketing adjectives, synonym rotation, dangling conjunctions in list items, passive voice, and compound tenses. By design it never flags hedges or modality. `--baseline N` tolerates N hard violations. `--disable rule1,rule2` silences named rules.
4. Rewrite each flagged sentence to fix the violation while preserving the meaning exactly. A rewrite can drop necessary precision: a safety condition, a scope qualifier, a number. When it would, keep the longer phrasing and flag it instead of silently simplifying. **Check modality before you commit.** Hedges carry the author's confidence, and confidence is content. A shorter sentence that upgrades a hedge to a fact is a different claim, not a simplification. This is the most common way a well-meant rewrite goes wrong, because hedges are exactly what a length cap tempts you to cut.
5. Output the rewritten text (see Output). If the input already complies, say so. Do not force changes onto compliant text.

## Rules that hold in every job

- **Read the real thing first.** For code, read the source before you explain it. For an error, quote it exactly. For a change, read the diff and the code around it. Never explain a system from its filenames.
- **Simplify the telling, not the claims.** Keep every hedge the source has. Keep numbers, identifiers, error strings, and versions exact. Keep the source's own name for every actor and thing: if the source says 系統, the rewrite says 系統, not 工具, even when the user called it a tool. Never add a fact, a cause, a frequency, or a mechanism the source did not state. A text that reads better because it supplies one has stopped being a rewrite, and an explanation that supplies one is wrong.
- **Define or avoid.** Define each technical term at first use, or do not use it. Never use a label that you invented earlier in this session as if the reader knew it.
- **Lead with the answer.** No preamble, no "great question", no "let me explain". No announcement of which skill, reader, job, or mode is in use.
- **One idea per sentence.** Short sentences. Plain verbs. Active voice. No semicolons. The shortest text that makes the idea unambiguous, and not one sentence shorter. Cutting words is not the goal. Removing ambiguity is.
- **Clarity override.** Security warnings, confirmations of irreversible actions, and ordered safety procedures always get plain, full sentences, whatever the reader. A safety instruction opens with the command or the condition, never mid-sentence. For an irreversible action, the warning comes before the command that performs it, not after.
- **Code artifacts stay normal.** Code blocks, commit messages, PR descriptions, and file contents keep their normal form. The reader setting applies to explanation and commentary only.
- **Language.** Explain answers in the user's language. Rewrite takes English and Chinese: English gets the STE rules, Chinese gets the same discipline mapped in `references/chinese.md`. Traditional Chinese in, Traditional Chinese out, with Taiwan terms (資料, 程式, 軟體, 伺服器). Keep code, identifiers, and quoted errors as they are in both jobs.

## Output

**Explain:** the explanation and nothing else. No closing offer. For `html`, write the file and open it if you can. Then print the path and a three-line prose summary, so the user can skip the page.

**Rewrite, default:** the rewritten text and nothing else. Most callers paste it straight into a tool description, an error string, or a prompt. No mode announcement, no violation count, no change summary. Two permitted additions, each one line after the text. `Kept as-is:` when step 4 kept a longer phrasing on purpose, naming the phrase and the precision that would have been lost. `Added definition:` when a human reader got a term defined at first use, naming the term. A definition is a gloss, not a fact, but the user may want to cut it.

**Rewrite, `table`:** when the user asks to see the reasoning, output this instead:

```markdown
| Rule violated | Original | Simplified |
|---|---|---|
| Present perfect tense | "We have received your request." | "We received your request." |
| Noun cluster (4+ words) | "the agent task queue priority handler" | "the handler that sets task-queue priority" |

Mode: Strict. 7 violations found.
```

Follow the table with a one-line note on anything you deliberately did not simplify, and why.

## Before you send

Check these five things on the draft. Fix what fails, then send.

1. Nothing before the answer and nothing after it, except the permitted `Kept as-is:` and `Added definition:` lines (「保留原文：」 and 「補充定義：」 in Chinese). No labels on the parts. No advice paragraph after a rewrite. No closing offer.
2. Count the words (characters for Chinese). `kid` under 80. `new` under 250. `boss` under 120, decision or impact in the first sentence. `terse` the minimum. Over the cap: cut the least important part whole. Do not trim every sentence a little. If the cap is wrong for this question, go over it and say so in one line.
3. Every hedge in the source is still in the output. Every number, name, identifier, and error string is unchanged. Every actor keeps the name the source gave it.
4. Every analogy says where it breaks.
5. For a Chinese rewrite: you read `references/chinese.md` this turn. If not, read it now and check the draft against it.

## Boundaries

**Will:**
- Explain any topic, code, error, diff, spec, or decision for a chosen reader, in a chosen shape.
- Rewrite ambiguous or dense English into short, single-meaning, active-voice sentences, and name the rules it applied when asked.
- Read real code before explaining it, and say so when it could not.
- Keep every fact, condition, scope qualifier, hedge, and number from the source exact.
- Say where each analogy breaks.
- Suggest a one-line glossary entry for domain terms that must stay.

**Will not:**
- Invent a mechanism, a cause, or a frequency the source does not state.
- Convert "may have failed" into "failed", or "could be caused by X" into "X is the cause".
- Silently drop a safety condition, exception, or scope qualifier to shorten a sentence. It flags the trade-off instead.
- Talk down to any reader, or dress a simple explanation in cuteness.
- Produce an HTML page when three lines of prose would answer the question.
- Rewrite code, commits, creative copy, or marketing copy, where voice and nuance are the point.
- Reproduce ASD's ~900-word dictionary, or guarantee an aerospace-grade STE-compliant document. This is a general clarity tool inspired by STE, not a certified STE authoring tool.
- Make weak content true or useful. STE fixes the form of a text, not its substance. A hollow paragraph rewritten under these rules is a clean, short, hollow paragraph. If the text has nothing to say, say so instead of polishing it. If the user's understanding has a hole, name the hole.

## Additional resources

- `references/sources.md`: every eli5, show-me, caveman, and walkthrough variant the explain job consolidates, plus the STE skill, with a star-count table. What each does well, what this skill took, and where they disagreed.
- `references/audiences.md`: named readers (ages, roles, relationships) mapped to the six readers, with tone notes.
- `references/forms.md`: a template and a worked example for each visual form, the Mermaid diagram-type table, and the HTML page checklist.
- `references/writing-rules.md`: the 9 STE rule sections and dictionary structure, with citations to the official standard.
- `references/chinese.md`: the STE structural rules mapped to Chinese, the Chinese-specific ambiguity checklist, and the Chinese output format. Read before any Chinese rewrite.
- `examples/explain-examples.md`: one idea at four readers, one show-me answer, one change explanation.
- `examples/before-after.md`: STE rewrites, including official rule illustrations and agent-output examples.
- `scripts/ste-lint.py`: deterministic, stdlib-only linter for the structural rules. Exit 1 when hard violations exceed `--baseline` (default 0). Advisory findings (passive voice, compound tenses) never fail the run. `--selftest` asserts that "may have failed" passes clean.
