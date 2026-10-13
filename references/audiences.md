# Audiences: from a named reader to a level

The user names a reader. Map it to one of the four human readers, then adjust tone and analogy source. The fifth reader, `agent`, is not in this table: it is a program or a model, and it gets the rewrite job's Strict rules instead of a tone. The level sets vocabulary, analogy use, and length. The tone note sets how it sounds.

## The four human readers

| Level | Reader | Vocabulary | Analogies | Length | Opening move |
|---|---|---|---|---|---|
| `kid` | A child, or anyone who asked for the literal five-year-old version | Everyday words only | One, carried through the whole answer | Three to six short sentences | The analogy |
| `new` | An adult with no background in this topic | Plain words. Define each term at first use. | One, when the idea is not obvious | One short paragraph, or a few | The what |
| `peer` | Someone who works in this field | Exact terms, no definitions | None unless asked | As long as the question needs | The answer |
| `boss` | Someone who decides and pays, and does not build | Impact, risk, cost, options, numbers | Only when it shortens the answer | Short. One screen. | The decision or the impact |

## Named readers

| The user says | Level | Tone | Analogies from |
|---|---|---|---|
| "a 5 year old", "my kid", "my daughter", "a child" | `kid` | Warm, patient, concrete. No exclamation marks the content did not earn. | Toys, food, playground, pets, bedtime |
| "a 10 year old", "middle school" | `kid` | Direct, a bit more detail, one real term allowed if defined | School, games, sports, phones |
| "a teenager", "high school" | `new` | Direct, no condescension, real terms defined | Social apps, games, school projects |
| "like I'm 5", "eli5", "dumb it down" | `new` | Plain and respectful. Zero background, not zero intelligence. | Daily life: kitchen, post office, traffic, library |
| "I know nothing about X" | `new` | Same as above | Whatever the user does know, if they said |
| "my mom", "my dad", "my parents", "my grandfather" | `new` | Warm, unhurried, no jargon at all | Household, shopping, banking, mail, cooking |
| "my partner", "my spouse", "a friend" | `new` | Casual, honest about what is hard | Shared life: planning a trip, splitting a bill, a shared calendar |
| "a college student" | `new` or `peer` | Confident. `peer` if their major is this field. | Courses, campus systems, part-time jobs |
| "a grad student", "a PhD student" | `peer` | Precise. Trade-offs and limits matter. | Their own field, if named |
| "a new hire", "a junior dev", "someone new to the codebase" | `new` with `peer` vocabulary | Define project-specific names. Assume general programming knowledge. | Other systems they likely know |
| "an engineer", "a senior engineer", "my team" | `peer` | Exact. Edge cases, failure modes, costs. | None |
| "a designer" | `boss` | What the user sees and feels. What constrains the design. | Interfaces they use |
| "a product manager", "a PM" | `boss` | What users notice, what it costs, what it unblocks, what the options are | Product trade-offs they already make |
| "my manager", "my boss", "a director", "a VP", "leadership" | `boss` | Decision first. Impact, risk, cost, timeline. Numbers where they exist. | Business processes, budgets, staffing |
| "a customer", "a non-technical client" | `boss` | What changes for them, what they need to do, what it costs | Their own business |
| "a lawyer", "a doctor", "an accountant" | `new` with their field's analogies | Respectful of their expertise, plain about ours | Contracts, diagnosis, ledgers |

## Tone rules that apply to every level

- Never talk down. A simple explanation respects the reader. A cute one does not.
- Match energy to content. A security hole is not "fun".
- Use the reader's own frame. A PM thinks in users and releases. A director thinks in risk and cost. A parent thinks in daily life.
- When the reader is a group ("my team", "the board"), write for the least-expert member who still has to act on the answer.

## When the user names no reader

A bare `/clarify` opens the menu in `SKILL.md` Step 0, which asks once. Outside the menu, use `new`. If the conversation makes it obvious that the user is an expert asking a narrow question, use `peer`. Do not ask who the reader is a second time. A wrong guess costs one follow-up. A repeated question costs one round trip every time.
