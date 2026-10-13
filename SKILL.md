---
name: clarify
description: "Make any text or idea clear for one chosen reader. Explain a topic, code, error, diff, or decision for a kid, a newcomer, a peer, or a boss. Shapes: prose, pseudocode, a tree, a Mermaid diagram, a diff, code, or one HTML page. Or rewrite English that a machine or another agent must parse without a human into ASD-STE100 Simplified Technical English. That covers tool descriptions, error messages, inter-agent instructions, and system prompts. Replaces the eli5, show-me, feynman, walkthrough, and STE100 skills. Triggers: explain, eli5, explain like I'm 5, explain this to my <someone>, dumb it down. Also: show me, draw it, diagram this, walk me through, how does X work, quiz me. Also: disambiguate, STE100, Simplified Technical English, plain-language rewrite, rewrite so an agent cannot misread this. Partial matches count. Not for creative or marketing copy."
version: 1.0.0
---

# Clarify

Every eli5, show-me, explain-to-my-boss, Feynman, walkthrough, and plain-language rewrite skill does one thing. It takes something and makes it clear to one specific reader. The skills differ on four choices only. Who the reader is. Whether the input is a question to answer or a text to transform. What shape the answer takes. How deep it goes. This skill names those four choices, gives each a default, and lets the user set any of them with one word.

It absorbs two families. The explain family (eli5, show-me, Feynman, walkthrough, and their variants, listed in `references/sources.md`) became the **explain** job. The ASD-STE100 rewrite skill this repository started as became the **rewrite** job and the `agent` reader.

## Invocation

```
/clarify [reader] [job] [form] [mode] <topic, question, text, file, error, or diff>
```

Every option word is optional, in any order. Natural language maps onto the same words.

| Choice | Words | Default |
|---|---|---|
| Reader | `kid`, `new`, `peer`, `boss`, `agent` | `new` for a question. `agent` for text a program will parse. `peer` for other text. |
| Job | `explain`, `rewrite` | Inferred from the input (Step 1) |
| Form (explain only) | `prose`, `pseudo`, `tree`, `diagram`, `diff`, `code`, `html` | `auto` |
| Mode (explain only) | `explain`, `change`, `map`, `analogies`, `quiz`, `gaps`, `break` | `explain` |
| Output (rewrite only) | `text`, `table` | `text` |

Phrase mapping. "eli5", "like I'm 5", "my kid" set `kid`. "Dumb it down", "I know nothing about this" set `new`. "Explain to my manager", "tell my boss" set `boss`. "For an agent", "so a model cannot misread this", "tool description", "STE100" set `agent`. "Show me", "draw it", "diagram this", "what does the flow look like" set form `auto` with a visual preference. "Walk me through" sets `tree` or `diagram`. "Quiz me" sets `quiz`. "Here is my understanding, what am I missing" sets `gaps`. "Show the diff", "which rules did it break", "before/after" set output `table`.

## Step 1: Pick the job

**Explain** when the user wants to understand something. That is a question, a topic, a piece of code, an error they hit, or a diff they have to review. The input is the subject. The output is new text about it.

**Rewrite** when the user hands over text and wants the same content in a clearer form. The asks sound like "rewrite this", "disambiguate this", "apply STE", "simplify this error message", or "clean up this tool description". The input is the material. The output must say exactly what the input said, and nothing more.

"eli5 this error" is explain. "Rewrite this error so an agent cannot misread it" is rewrite. When both readings are possible, explain. A missed rewrite costs one follow-up. A missed question loses what the user wanted to know.

## Step 2: Pick the reader

Decide who reads the answer before you write a word. The reader sets vocabulary, analogy use, length, and for `agent`, the whole rule set.

| Reader | Assume | Vocabulary | Analogies | Length |
|---|---|---|---|---|
| `kid` | No background and a short attention span | Everyday words only. No jargon at all. | One, carried through the whole answer | Very short |
| `new` | No background in this topic, full adult intelligence | Plain words. Define each technical term at first use. | One, when the idea is not obvious | Short |
| `peer` | Works in this field | Exact terms. Trade-offs and edge cases matter. | None, unless asked | As long as the question needs |
| `boss` | Decides and pays, does not build | Impact, risk, cost, options. Numbers where they exist. | Only if it shortens the answer | Short. Decision first. |
| `agent` | A model, a tool, a translation pipeline, or a non-native reader, with no human to ask | The plainest word, used the same way every time | None | As long as it takes to be unambiguous, and not one word longer |

Rules for picking the reader:

- **Default to `new` for a question.** The best-known eli5 skill says "like a 5 year old" in its title. Its body says "someone who knows nothing about this topic". The body is right. Assume zero background, not zero intelligence. Use `kid` only when the user names a child, an age under 12, or asks for the literal five-year-old version.
- **Default to `agent` for text a program will parse.** That means tool and function descriptions, error strings, inter-agent instructions, system prompts, and status reports. It also means any output another model reads with no human in the loop. A technician on a tarmac and a model parsing a tool description are in the same position: neither can ask what you meant.
- Map named readers to the nearest level, then adjust tone. "My mom", "my partner": `new` with a warm tone and analogies from daily life. "A grad student": `new` or `peer`, depending on whether their field is this field. "A designer", "a PM", "a director": `boss`, framed around what that role decides. The full table is in `references/audiences.md`.
- **Never talk down.** A simple explanation respects the reader. A cute one does not. No baby talk, no "imagine a magical box", no exclamation marks the content did not earn.
- **Sticky mode.** The user can say "eli5 mode", "simple mode", or "keep explaining like this". Then keep the reader for the rest of the session. Drop it when they say "normal mode" or "stop eli5". Confirm the change in two words. Otherwise each call is one-shot.

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

- Put the visual next to the two or three lines of text it supports. Text first, then visual, then the one line that says what to notice.
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

Typical cuts: `peer` usually gets 1, 3, 4. `boss` usually gets 1, 5, 4, with 5 before 4. `kid` gets 1, 2, 6.

**`change`.** For a diff, a PR, a spec, or a proposed design, read by someone who has not seen the code or the session. Use these headings, in this order:

1. **Problem.** What goes wrong today, in terms of what a user or operator sees. Why the current design causes it.
2. **Solution.** The new behavior as a before/after flow. Introduce each component by what it is responsible for, not by its filename.
3. **What changes.** Every schema, API, config, or other durable contract that the change adds, changes, removes, or resets. Say "none" explicitly when there is none. Call out migrations, destructive resets, and compatibility behavior even when users will never see them.
4. **What people will notice.** One short sentence.

Separate what exists today from what is only proposed. The text must stand on its own without the diff.

**Other modes.**

- `map`: the user does not yet know what to ask. List 5 to 8 things worth understanding about the topic, each as a question, foundations first.
- `analogies`: give 3 to 5 analogies, say where each one breaks, then name the best one in a single memorable line.
- `quiz`: ask one question at a time, wait for the answer, raise the difficulty each round. Correct wrong answers in two lines, then ask the next question.
- `gaps`: the user explains the topic to you. Find the exact point where their explanation goes vague or wrong, name it, ask one question that tests it, then give the corrected version.
- `break`: for a design or decision. Name the hidden assumption it rests on and the edge case nobody tested.

## Step 4: Rewrite

The rewrite job applies ASD-STE100, the controlled-language standard the aerospace and defense industry built so that maintenance instructions cannot be misread. STE removes the two biggest sources of misreading: words with more than one meaning, and sentences with more than one possible structure. `references/writing-rules.md` has the full rule summary and citations.

### Two modes, set by the reader

**Strict**, for reader `agent`: apply every rule below, including the length caps and the one-word-one-meaning discipline. Anywhere a wrong reading has a cost.

**Flavored**, for a human reader (`new`, `peer`, `boss`): apply the structural rules in full and treat the lexical rules as advisory. Keep the length caps, active voice, simple tenses, no phrasal verbs, no semicolons, no nominalization, no marketing adjectives. Drop the one-word-one-meaning lockdown. Prose needs some range, and a strict rewrite of prose reads as a personality transplant rather than a clarification. Then apply the reader's vocabulary rule from Step 2: define terms for `new`, keep them for `peer`, lead with impact for `boss`.

The split between structural and lexical rules is the split between what this skill can verify and what needs ASD's dictionary. STE's ~900-word approved dictionary is free to obtain but not free to redistribute. Issue 9, page 2, grants reproduction rights to eight listed categories of organisation. This project is in none of them. So this skill applies the dictionary's *principle*, the plainest word used the same way every time, and never claims dictionary compliance. For certified STE documentation, request the standard from the [official downloads page](https://www.asd-ste100.org/STE_downloads.html) and check word by word.

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
- **Simplify the telling, not the claims.** Keep every hedge the source has. Keep numbers, identifiers, error strings, and versions exact. Never add a fact, a cause, a frequency, or a mechanism the source did not state. A text that reads better because it supplies one has stopped being a rewrite, and an explanation that supplies one is wrong.
- **Define or avoid.** Define each technical term at first use, or do not use it. Never use a label that you invented earlier in this session as if the reader knew it.
- **Lead with the answer.** No preamble, no "great question", no "let me explain". No announcement of which skill, reader, job, or mode is in use.
- **One idea per sentence.** Short sentences. Plain verbs. Active voice. No semicolons. The shortest text that makes the idea unambiguous, and not one sentence shorter. Cutting words is not the goal. Removing ambiguity is.
- **Clarity override.** Security warnings, confirmations of irreversible actions, and ordered safety procedures always get plain, full sentences, whatever the reader. A safety instruction opens with the command or the condition, never mid-sentence.
- **Code artifacts stay normal.** Code blocks, commit messages, PR descriptions, and file contents keep their normal form. The reader setting applies to explanation and commentary only.
- **Language.** Explain answers in the user's language. Rewrite works on English text, because STE is a standard for English. Keep code, identifiers, and quoted errors as they are in both jobs.

## Output

**Explain:** the explanation and nothing else. No closing offer. For `html`, write the file and open it if you can. Then print the path and a three-line prose summary, so the user can skip the page.

**Rewrite, default:** the rewritten text and nothing else. Most callers paste it straight into a tool description, an error string, or a prompt. No mode announcement, no violation count, no change summary. One permitted addition. If step 4 kept a longer phrasing on purpose, add one line after the text, prefixed `Kept as-is:`. Name the phrase and the precision that would have been lost.

**Rewrite, `table`:** when the user asks to see the reasoning, output this instead:

```markdown
| Rule violated | Original | Simplified |
|---|---|---|
| Present perfect tense | "We have received your request." | "We received your request." |
| Noun cluster (4+ words) | "the agent task queue priority handler" | "the handler that sets task-queue priority" |

Mode: Strict. 7 violations found.
```

Follow the table with a one-line note on anything you deliberately did not simplify, and why.

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

- `references/sources.md`: every eli5, show-me, feynman, and walkthrough variant the explain job consolidates, plus the STE skill. What each does well, what this skill took, and where they disagreed.
- `references/audiences.md`: named readers (ages, roles, relationships) mapped to the five readers, with tone notes.
- `references/forms.md`: a template and a worked example for each visual form, the Mermaid diagram-type table, and the HTML page checklist.
- `references/writing-rules.md`: the 9 STE rule sections and dictionary structure, with citations to the official standard.
- `examples/explain-examples.md`: one idea at four readers, one show-me answer, one change explanation.
- `examples/before-after.md`: STE rewrites, including official rule illustrations and agent-output examples.
- `scripts/ste-lint.py`: deterministic, stdlib-only linter for the structural rules. Exit 1 when hard violations exceed `--baseline` (default 0). Advisory findings (passive voice, compound tenses) never fail the run. `--selftest` asserts that "may have failed" passes clean.
