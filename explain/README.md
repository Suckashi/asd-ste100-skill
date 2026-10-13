# Explain — one skill for the whole eli5 / show-me family

A Claude Code skill that explains any topic, code, error, diff, or decision for a chosen reader, in the clearest shape. It replaces the separate `eli5`, `show-me`, `explain-to-my-boss`, Feynman, and walkthrough skills that circulate online with one skill and three option words.

Those variants differ on three things only:

| Axis | Words | Default |
|---|---|---|
| Who reads it | `kid`, `new`, `peer`, `boss` | `new` (zero background, adult) |
| What shape | `prose`, `pseudo`, `tree`, `diagram`, `diff`, `code`, `html` | `auto` (smallest view that makes the point) |
| How deep | `explain`, `change`, `map`, `analogies`, `quiz`, `gaps`, `break` | `explain` |

They agree on the rest. Never talk down. Say where the analogy breaks. Read the real code first. Keep hedges and error strings exact. Write safety warnings plainly. Keep code artifacts normal. Those shared rules hold in every mode. `references/sources.md` lists each variant, what it does well, what this skill took, and where the variants disagreed and what this skill decided.

## Install

With the [skills CLI](https://skills.sh/), from your project root:

```bash
npx skills add suckashi/asd-ste100-skill --skill explain
```

Or clone and copy the folder:

```bash
git clone https://github.com/suckashi/asd-ste100-skill /tmp/asd-ste100-skill
cp -r /tmp/asd-ste100-skill/explain ~/.claude/skills/explain
```

## Use

```
/explain <topic>                         zero-background adult, auto shape
/explain kid what is a database index    the literal five-year-old version
/explain boss diagram how login works    decision-first, with a sequence diagram
/explain peer diff this refactor         exact terms, shown as a diff
/explain change                          Problem / Solution / What changes, for the current diff
/explain html how a B-tree splits        one self-contained page, big visuals, few words
/explain quiz TCP handshake              one question at a time
/explain gaps                            then explain the topic yourself, and it finds the hole
```

Natural language works the same. Try "eli5 this error" or "show me what happens when I submit the form". Or "explain this PR to my manager" or "walk me through the checkout flow".

Say "eli5 mode" or "simple mode" to keep a level for the rest of the session. Say "normal mode" to drop it.

### Optional aliases

If you want `/eli5` and `/show-me` as their own commands, add two one-line command files. They route to this skill with a preset.

```bash
mkdir -p ~/.claude/commands
echo 'Use the explain skill with audience new. Topic: $ARGUMENTS' > ~/.claude/commands/eli5.md
echo 'Use the explain skill with a visual form (tree, diagram, diff, or html, whichever is smallest). Topic: $ARGUMENTS' > ~/.claude/commands/show-me.md
```

## Files

- `SKILL.md`: the skill.
- `references/sources.md`: the consolidation. Every variant, what was taken, what was decided.
- `references/audiences.md`: named readers (ages, roles, relationships) mapped to the four levels, with tone notes.
- `references/forms.md`: a template and an example for each shape, the Mermaid type table, the HTML checklist.
- `examples/worked-examples.md`: one idea at four levels, one show-me answer, one change explanation.

## Credits

This skill merges work by Thariq Shihipar (the official `eli5`), SJ-Wu (`eli5`, `show-me`), DreambigOu (`ELI5`), and nathanksou (`eli5`, after Julius Brussee's Caveman). It also draws on dzhng (`eli5` for specs), haunguyendev (`feynman-skill`), alexanderop (`walkthrough`), dogum (`html-artifacts`), and the Mermaid skill authors listed in `references/sources.md`. The merge, the three-axis framing, and the decisions in the disagreement table are original to this repo.
