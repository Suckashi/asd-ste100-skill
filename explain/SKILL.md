---
name: explain
description: "Explain any topic, code, error, change, or decision for a chosen reader. Shapes: short prose, analogy, pseudocode, call tree, file tree, Mermaid diagram, diff, or a single HTML page. One skill that covers the eli5, show-me, explain-to-my-boss, feynman, and walkthrough variants. Triggers: explain, eli5, explain like I'm 5, explain this to my <someone>, dumb it down, simple mode. Also: show me, draw it, diagram this, visualize this, walk me through, how does X work. Also: what does this do, teach me, quiz me. Partial matches count. Not for writing documentation, commit messages, or marketing copy."
version: 0.1.0
---

# Explain

Dozens of `eli5`, `show-me`, `explain-to-my-boss`, Feynman, and walkthrough skills exist. They differ on three things only: **who reads the answer**, **what shape the answer takes**, and **how deep it goes**. This skill names those three axes, gives each a default, and lets the user set any of them with one word. `references/sources.md` lists every variant this skill absorbed and what it took from each.

## Invocation

```
/explain [audience] [form] [mode] <topic, question, file, error, or diff>
```

All three option words are optional and can come in any order. Natural language works the same way: "explain this to my manager with a diagram" sets audience `boss` and form `diagram`.

| Axis | Words | Default |
|---|---|---|
| Audience | `kid`, `new`, `peer`, `boss` | `new` |
| Form | `prose`, `pseudo`, `tree`, `diagram`, `diff`, `code`, `html` | `auto` |
| Mode | `explain`, `change`, `map`, `analogies`, `quiz`, `gaps`, `break` | `explain` |

Phrases map onto the same words. "eli5", "like I'm 5", "my kid" set `kid`. "Dumb it down", "I know nothing about this" set `new`. "Explain to my manager", "tell my boss" set `boss`. "Show me", "draw it", "diagram this", "what does the flow look like" set form `auto` with a visual preference. "Walk me through" sets `tree` or `diagram`. "Quiz me" sets `quiz`. "Here is my understanding, what am I missing" sets `gaps`.

## Step 1: Pick the reader

Decide who reads the answer before you write a word. The reader sets vocabulary, analogy use, and length.

| Level | Assume | Vocabulary | Analogies | Length |
|---|---|---|---|---|
| `kid` | No background and a short attention span | Everyday words only. No jargon at all. | One, carried through the whole answer | Very short |
| `new` | No background in this topic, full adult intelligence | Plain words. Define each technical term at first use. | One, when the idea is not obvious | Short |
| `peer` | Works in this field | Exact terms. Trade-offs and edge cases matter. | None, unless asked | As long as the question needs |
| `boss` | Decides and pays, does not build | Impact, risk, cost, options. Numbers where they exist. | Only if it shortens the answer | Short. Decision first. |

Rules for picking the level:

- **Default to `new`.** The best-known eli5 skill says "like a 5 year old" in its title. Its body says "someone who knows nothing about this topic". The body is right. Assume zero background, not zero intelligence. Use `kid` only when the user names a child, an age under 12, or asks for the literal five-year-old version.
- Map named readers to the nearest level, then adjust tone. "My mom", "my partner", "my grandfather": `new` with a warm tone and analogies from daily life. "A college student", "a grad student": `new` or `peer` depending on whether their field is this field. "A designer", "a PM", "a director": `boss`, framed around what that role decides. The full mapping table is in `references/audiences.md`.
- **Never talk down.** A simple explanation respects the reader. A cute one does not. No baby talk, no "imagine a magical box", no exclamation marks that the content did not earn.
- **Sticky mode.** The user can say "eli5 mode", "simple mode", or "keep explaining like this". Then keep the level for the rest of the session. Drop it when they say "normal mode" or "stop eli5". Confirm the change in two words. Otherwise each call is one-shot.

## Step 2: Pick the shape

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

## Step 3: Pick the depth

### `explain` (default)

Build the answer from these parts, in this order. Drop any part that would add no information for this reader.

1. **What.** One sentence. The thing, in the reader's words.
2. **Analogy.** Something the reader already knows that behaves the same way. Say where the analogy breaks. An analogy with no stated limit teaches a wrong model.
3. **How.** The mechanism, at the depth this reader needs. For code, purpose before mechanism: what it is for, then how it does it.
4. **Example.** One concrete scenario, from trigger to outcome, with real values.
5. **So what.** Why this reader should care. What they can now do, decide, or stop worrying about.
6. **Takeaway.** One sentence the reader can repeat to someone else.

Typical cuts: `peer` usually gets 1, 3, 4. `boss` usually gets 1, 5, 4, with 5 before 4. `kid` gets 1, 2, 6.

### `change`

For a diff, a PR, a spec, or a proposed design. The reader has not seen the code or the session. Use these headings, in this order:

1. **Problem.** What goes wrong today, in terms of what a user or operator sees. Why the current design causes it.
2. **Solution.** The new behavior as a before/after flow. Introduce each component by what it is responsible for, not by its filename.
3. **What changes.** Every schema, API, config, or other durable contract that the change adds, changes, removes, or resets. Say "none" explicitly when there is none. Call out migrations, destructive resets, and compatibility behavior even when users will never see them.
4. **What people will notice.** One short sentence.

Separate what exists today from what is only proposed. The text must stand on its own without the diff.

### Other modes

- **`map`**: the user does not yet know what to ask. List 5 to 8 things worth understanding about the topic, each as a question, foundations first.
- **`analogies`**: give 3 to 5 analogies, say where each one breaks, then name the best one in a single memorable line.
- **`quiz`**: ask one question at a time, wait for the answer, raise the difficulty each round. Correct wrong answers in two lines, then ask the next question.
- **`gaps`**: the user explains the topic to you. Find the exact point where their explanation goes vague or wrong, name it, ask one question that tests it, then give the corrected version.
- **`break`**: for a design or decision. Name the hidden assumption it rests on and the edge case nobody tested.

## Rules that hold in every mode

- **Read the real thing first.** For code, read the source before you explain it. For an error, quote it exactly. For a change, read the diff and the code around it. Never explain a system from its filenames.
- **Simplify the telling, not the claims.** Keep every hedge the source has. Keep numbers, identifiers, error strings, and versions exact. A simpler sentence that promotes "may fail" to "fails" is a different claim, not a clearer one.
- **Define or avoid.** Define each technical term at first use, or do not use it. Never use a label that you invented earlier in this session as if the reader knew it.
- **Lead with the answer.** No preamble, no "great question", no "let me explain", no announcement of which skill, level, or mode is in use.
- **One idea per sentence.** Short sentences. Plain verbs. Active voice. The shortest answer that makes the idea click, and not one sentence shorter.
- **Clarity override.** Security warnings, confirmations of irreversible actions, and ordered safety procedures always get plain, full sentences, whatever the level. Resume the chosen level after.
- **Code artifacts stay normal.** Code blocks, commit messages, PR descriptions, and file contents keep their normal form. The level applies to explanation and commentary only.
- **Answer in the user's language.** Keep code, identifiers, and quoted errors as they are.

## Output

The explanation and nothing else. No closing offer. For `html`, write the file and open it if you can. Then print the path and a three-line prose summary, so the user can skip the page.

## Boundaries

**Will:**
- Explain any topic, code, error, diff, spec, or decision at a chosen level and in a chosen shape.
- Read real code before explaining it, and say so when it could not.
- Keep every fact, hedge, and number from the source exact while simplifying everything around it.
- Say where each analogy breaks.

**Will not:**
- Invent a mechanism, a cause, or a frequency the source does not state.
- Talk down to any reader, or dress a simple explanation in cuteness.
- Produce an HTML page when three lines of prose would answer the question.
- Rewrite code, commits, docs, or marketing copy in the simplified voice.
- Make a wrong idea sound right. If the user's understanding has a hole, name the hole.

## Additional resources

- `references/sources.md`: every eli5, show-me, feynman, and walkthrough variant this skill consolidates, what each does well, and what this skill took from it.
- `references/audiences.md`: the mapping from named readers (ages, roles, relationships) to the four levels, with tone notes.
- `references/forms.md`: a template and a worked example for each visual form, plus the Mermaid diagram-type table and the HTML page checklist.
- `examples/worked-examples.md`: one concept at all four levels, one show-me answer, one change explanation.
