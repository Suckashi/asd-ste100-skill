# Clarify — one skill for "explain this" and "rewrite this so it cannot be misread"

A Claude Code skill with one command, `/clarify`, that replaces two families of skills. The first family is the explainers: `eli5`, `show-me`, `explain-to-my-boss`, caveman, walkthrough, and their many variants. The second is the [ASD-STE100 Simplified Technical English](https://www.asd-ste100.org/) rewrite skill this repository started as. It turns dense, ambiguous English or Chinese into text that a person or a machine cannot misread. STE was written for people first, aircraft technicians reading English as a second language. The full rule set goes to a machine reader, the softer "80%" mode goes to a human reader.

All of them do one thing: take something and make it clear to one specific reader. So the skill picks the reader first, then the job, then the shape and the depth. Each choice has a default and a one-word override.

The shape of the skill follows a note Andrej Karpathy posted on 2 October 2026 ([x.com/karpathy/status/2105819303471976479](https://x.com/karpathy/status/2105819303471976479)). He wrote that we will spend much more time trying to understand the outputs of language models, and listed four ways to make that easier, in rising order of effort: ask for ASD-STE100 prose (or "80% of the way there"), ask for diagrams, ask for an interactive HTML page, ask for a 3Blue1Brown-style explainer video. The first three are this skill's rewrite job, its `diagram` and `tree` forms, and its `html` form. The fourth is out of scope. The cheat sheet attached to his post is not used here. Reviewers found errors in it, so the rules below come from the standard's own rule categories (see `references/writing-rules.md`).

## Two jobs, six readers

| Reader | Who that is | What they get |
|---|---|---|
| `kid` | A child, or anyone who asked for the literal five-year-old version | One analogy, everyday words, a few sentences |
| `new` | An adult with no background in this topic (the default for a question) | Plain words, terms defined at first use, one analogy when needed |
| `peer` | Someone who works in this field | Exact terms, trade-offs, edge cases |
| `boss` | Someone who decides and pays, and does not build | Decision first, then impact, risk, cost, options |
| `terse` | Someone who wants the fewest words (the caveman voice) | Answer, reason, next step. Articles dropped, negations and numbers kept. |
| `agent` | A model, a tool, a pipeline, or a non-native reader, with no human to ask (the default for text a program will parse) | The full ASD-STE100 Strict rule set |

**Explain** is the job when the user wants to understand something: a question, a topic, code, an error, a diff. **Rewrite** is the job when the user hands over text and wants the same content in a clearer form, for a person or for a machine, in English or in Chinese. The skill infers the job from the input. "eli5 this error" is explain. "Rewrite this error so an agent cannot misread it" is rewrite.

## Explain

The shape is the smallest view that makes the key point clear. The options are prose, pseudocode, a call, component, or file tree, a Mermaid diagram, a diff, or a code block. The last option is one self-contained HTML page with big visuals and few words. The depth is a fixed structure (what, analogy with its limit, how, example, so what, takeaway) with the parts this reader does not need dropped. The other mode is `change`, for a PR or spec (Problem, Solution, What changes, What people will notice).

`references/sources.md` lists every variant the explain job absorbed, what each does well, and what was taken. It ends with a table of where the variants disagree and what this skill decided. `examples/explain-examples.md` shows one idea at four readers, one show-me answer, and one change explanation.

## Rewrite: why STE, and why for agents

STE serves two readers here. A person who finds a paragraph dense gets the Flavored mode: the sentence discipline in full, the fixed vocabulary dropped, terms defined for a newcomer. A machine that must parse the text gets Strict. Chinese text gets the same structural rules mapped to Chinese in [`references/chinese.md`](references/chinese.md): actor as subject, one instruction per sentence, no dropped subjects, hedges such as 可能 kept, 進行 plus noun turned back into the verb. Traditional Chinese in, Traditional Chinese out, with Taiwan terms.

ASD-STE100 exists because a misread instruction on an aircraft can kill people. The intended readers were often not native English speakers, with no author to call for clarification. The standard's fix: one meaning per word, active voice, simple tenses, one instruction per sentence, short sentences, no dropped words.

An LLM agent parsing another agent's output is in a strikingly similar position. No back-channel, no way to ask "did you mean X or Y?" The rules that keep a mechanic from misreading a torque spec are the same rules. They keep a downstream agent from misreading a tool description or an inter-agent message. That is why `agent` is a reader in this skill, and why it switches on the whole Strict rule set.

| Before | After |
|---|---|
| "This tool will attempt to synchronize state across the various backends that have been configured, and if a conflict is detected it may resolve it automatically depending on the strategy that has been set, or otherwise it will surface the conflict for manual review." | "The tool tries to synchronize state across the configured backends. If it finds a conflict, it reads the configured strategy. If the strategy allows automatic resolution, the tool may resolve the conflict without a user. If the tool does not resolve the conflict, it reports the conflict for manual review." |
| "An error may have occurred while processing your request due to a possible mismatch in the expected data format, which could be caused by an outdated client version." | "Your request may have failed. The cause may be a data format that does not match what the server expects. An outdated client can cause this mismatch. Check your client version." |

More examples, including illustrations of the official STE rules themselves, in [`examples/before-after.md`](examples/before-after.md).

The rewrite job:

1. Picks a mode from the reader. **Strict** for `agent`. **Flavored** for a human reader: the sentence discipline in full, the fixed-vocabulary lockdown dropped.
2. Reads the input for meaning.
3. Flags every rule violation sentence by sentence. The checks: ambiguous word choice, compound tense, passive voice with an unclear actor, multi-instruction sentences, oversized noun clusters, dropped words. Also sentences over length, phrasal verbs, nominalized actions, semicolons, hedge stacks, and marketing adjectives.
4. Rewrites each flagged sentence without dropping any fact, condition, hedge, or scope qualifier. If a shorter phrasing would lose required precision, it keeps the longer phrasing and flags the trade-off.
5. Outputs the rewritten text on its own, plus a one-line `Kept as-is:` note when it deliberately left something unsimplified. Ask for "the diff" or "which rules did it break" and it outputs a before/after table naming each rule instead.

The structural rules are mechanical. You can point at the word or punctuation mark that breaks each one. The rules that depend on ASD's dictionary are flagged as advisory rather than enforced. The skill does **not** reproduce ASD's official ~900-word approved dictionary. The standard is free to obtain but not free to redistribute. Issue 9 permits reproduction only with ASD's written authority, or by eight listed categories of organisation. This project belongs to none of them. For certified STE-compliant documentation, use the real standard. Full rule summary and citations: [`references/writing-rules.md`](references/writing-rules.md).

### The linter

`scripts/ste-lint.py` is a deterministic, stdlib-only check for the structural rules. It checks semicolons, a short list of soft phrasal verbs (spin up, reach out, dive into, kick off, circle back, touch base), nominalizations, and marketing adjectives. It also checks passive voice, present-perfect forms (including irregular participles such as "has run"), long sentences, synonym rotation, and dangling conjunctions in supported list items. It never flags hedges or modality: those are content, not style, and `--selftest` asserts that "may have failed" passes clean.

It does not compare an original text with a rewrite, verify that requirement strength stayed the same, or prove that a rewrite preserved meaning. It does not check noun-cluster length (that needs part-of-speech tagging), and it does not know phrasal verbs outside its list. A zero-violation result means that the configured structural checks found no problems.

The dangling-conjunction rule checks list markers at the start of a line with zero to three leading spaces. It supports `-`, `*`, `+`, and numeric markers ending in `.` or `)`. It does not parse list syntax inside blockquotes, lazy continuation, or full nested-list semantics. A stripped line beginning with three backticks or three tildes toggles fence detection. `examples/linter-edge-cases.md` is an intentionally invalid fixture: run `python scripts/ste-lint.py examples/linter-edge-cases.md` to confirm the two expected findings.

The skill's own `SKILL.md` does not lint clean, because its rule tables quote the patterns they forbid. Lint it with `python scripts/ste-lint.py --baseline 15 SKILL.md` and read the findings as examples, not defects.

## Installation

### Quick install (npx skills)

From your project root:

```bash
npx skills add suckashi/asd-ste100-skill
```

The [skills CLI](https://skills.sh/) sends anonymous install telemetry (skill name and timestamp). Set `DISABLE_TELEMETRY=1` to opt out. Update later with `npx skills update`.

### Clone

```bash
git clone https://github.com/suckashi/asd-ste100-skill ~/.claude/skills/clarify
```

### If you installed an earlier version

This repository used to ship two skills, `asd-ste100` and `explain`. Both are now `clarify`. Remove the old folders from `~/.claude/skills/` or `.claude/skills/` and install `clarify`. The old trigger phrases (`STE100`, `disambiguate`, `eli5`, `show me`) still work.

## Usage

Type `/clarify` and say what you want. When the request is clear, it answers at once. When it is not, it asks only about the part it cannot tell, as clickable choices in Claude Code or a numbered text menu elsewhere.

```
/clarify                                   asks all three, then asks for your text or question
/clarify <pasted error message>            asks one thing: explain it, or rewrite it?
/clarify make this simpler for my team     asks one thing: for whom?
/clarify how does login work               clear: answers at once
```

The three questions it can ask, with defaults first:

```
What do you want?  explain it · explain a change · rewrite for an agent · rewrite for people
For whom?          new · peer · boss · kid
What shape?        auto · diagram or tree · pseudocode, code, or diff · one HTML page
```

It asks once, never twice. Picks stick for the session. Say "menu" to choose again. Any option word or trigger phrase settles the choice up front, so the fast path never sees a question.

```
/clarify [reader] [job] [form] [mode] <topic, question, text, file, error, or diff>
```

```
/clarify what is a database index              explain, zero-background adult, auto shape
/clarify kid what is a database index          the literal five-year-old version
/clarify boss diagram how login works          decision first, with a sequence diagram
/clarify peer diff this refactor               exact terms, shown as a diff
/clarify change                                Problem / Solution / What changes, for the current diff
/clarify html how a B-tree splits              one self-contained page, big visuals, few words
/clarify terse why does this test flake        fewest words: answer, reason, next step

/clarify rewrite <paste a tool description>    STE Strict, rewritten text only
/clarify agent <paste an error message>        same: agent reader implies rewrite in Strict mode
/clarify new rewrite <paste a README section>  STE Flavored, terms defined for a newcomer
/clarify table rewrite <paste text>            the before/after rule table instead of the text
/clarify rewrite <貼一段中文>                   中文照同一套句子紀律改寫，繁體進繁體出
```

Or just say it. For explain: "eli5 this error", "show me what happens when I submit the form", "explain this PR to my manager", "walk me through the checkout flow". For rewrite: "disambiguate this tool description", "apply STE100 to this", "rewrite this so an agent can't misread it".

Say "eli5 mode", "simple mode", or "caveman mode" to keep a reader for the rest of the session. Say "normal mode" to drop it.

### Optional aliases

If you want `/eli5`, `/show-me`, or `/ste` as their own commands, add one-line command files that route to this skill with a preset:

```bash
mkdir -p ~/.claude/commands
echo 'Use the clarify skill with reader new. Topic: $ARGUMENTS' > ~/.claude/commands/eli5.md
echo 'Use the clarify skill with a visual form (tree, diagram, diff, or html, whichever is smallest). Topic: $ARGUMENTS' > ~/.claude/commands/show-me.md
echo 'Use the clarify skill with reader agent and job rewrite. Text: $ARGUMENTS' > ~/.claude/commands/ste.md
```

## Scope

Built for: questions about any topic, code, error, or change, for any human reader. And for agent-to-agent messages, tool and function descriptions, error messages, system prompts, and inter-agent instructions, for a machine reader.

Not built for: creative writing, marketing copy, or anything where voice and nuance are the point. STE is deliberately flat and literal, and a simplified explanation of a poem is not what anyone wants.

One limit worth stating up front: this fixes the form of a text, not its substance. A paragraph with nothing to say comes out short, clean, and still empty.

## Sources

- [`references/sources.md`](references/sources.md): the eli5, show-me, caveman, walkthrough, and HTML-artifact skills the explain job consolidates, with links and a star-count table. The visual forms come from HumanLayer's `show-me`. The reader axis comes from DreambigOu's `ELI5`. The `new` default and the HTML page come from the official `eli5`.
- [danyuchn/asd-ste100-skill](https://github.com/danyuchn/asd-ste100-skill): the STE skill this repository forked from.
- [ASD-STE100 official site](https://www.asd-ste100.org/)
- [ASD-STE100 — About STE](https://www.asd-ste100.org/about_STE.html)
- [ASD Europe — Simplified Technical English](https://www.asd-europe.org/standards-specifications/simplified-technical-english/)
- [Simplified Technical English — Wikipedia](https://en.wikipedia.org/wiki/Simplified_Technical_English)
- [TechScribe — ASD-STE100 Simplified Technical English](https://www.techscribe.co.uk/techw/asd-simplified-technical-english.htm)

## License

MIT — see [LICENSE](LICENSE).
