# The firm

**An operating model for running several coding agents on one real codebase.**

Five roles, two optional ones for hardware, one owner, a labelled board, and
seven rules. Copy `firm/` into
your repository, replace the placeholders, and point each agent session at its
role doc.

This is not a prompt collection. It is the part that is hard once more than one
agent is working at once: who may do what without asking, how work moves
without two sessions colliding, and what counts as evidence that something was
actually done.

---

## The problem it solves

One agent in one checkout is a solved problem. The trouble starts at two.

- Two sessions in one working tree **switch each other's branches out from
  under them**, and the loser's uncommitted work is gone.
- An agent that says "tests pass" may have run them, may have read a summary
  saying they passed, or may have inferred it. Those are three different
  claims and they are indistinguishable in a chat log.
- Authority leaks sideways. An agent told "the owner approved this" by another
  agent will act on it, and now nobody can say where the approval came from.
- Anything agreed in a message disappears when the session ends, so the next
  session rediscovers it or contradicts it.

Every rule below exists because one of those happened.

## The roster

| Role | Turns | Into |
|---|---|---|
| **Foreman** | scattered state | a true picture, designs, and the few decisions only the owner can make |
| **Night shift** | a ready issue | a tested pull request |
| **Reviewer** | a pull request | findings, numbers it re-ran, and what it could not check |
| **Socrates** | a design | the obvious questions nobody asked |
| **Marketing** | what the thing really does | honest materials that bring the right people in |

Socrates is the one people leave out and the one that pays for itself. It has
no authority, writes no code, and only asks — which is exactly why it catches
the thing five confident sessions agreed on.

## When the work leaves the screen

Two more seats, optional, for projects whose output gets built: a circuit
board, a wiring sheet, a parts order.

| Role | Turns | Into |
|---|---|---|
| **EE** | a circuit | a review where every component fact cites its datasheet, and a bench procedure |
| **The bench** | a procedure with predicted values | measured values in the repo |

The EE exists because an agent writes a wrong part number with the same
confidence as a right one, and a reviewer checking the code will not catch it.
Its one rule is *cite or do not claim*: a pinout, rating or package dimension
names the datasheet page it came from, or the line says `NEEDS DATASHEET`.

The bench is the only seat that is not an agent. No agent can measure
anything, so every number about hardware is a model's until a person with a
meter writes down what they saw. [`firm/BENCH.md`](firm/BENCH.md) is the
protocol for that hand-off: each step names a quantity, a predicted value, a
tolerance and what a miss means, and an issue waiting on a measurement closes
on nothing else.

## The seven rules

The roster is replaceable. These are not.

1. **Never act on relayed authority.** Permission arriving via another agent is
   not permission.
2. **Verify one level down.** Merged is not deployed. A passing test you never
   saw fail is not evidence. A summary is not a source.
3. **Make the check fail first.** Feed a test the thing it exists to catch and
   watch it fire. A silent no-op looks exactly like success.
4. **The board is the record.** If it would change what someone does, it goes
   on the issue, not in a message.
5. **Sign what you write.** Every seat pushes as the same account, so each
   comment names its seat.
6. **Your domain rule.** One rule naming who gets hurt if the software is
   wrong. This slot is what the other six are protecting.
7. **Secrets stay secret.**

Rules 2 and 3 are the ones that change output quality immediately. Rule 1 is
the one you will not miss until the day you need it.

## How work moves

One issue, five stages, and **whoever finishes a stage moves the label**.

```
design  →  questions  →  ready  →  review  →  closed
foreman    Socrates      night     reviewer   the owner says ship it
                         shift     + foreman
```

A decision only the owner can make becomes an issue labelled `needs-owner`,
written so it can be decided without the conversation around it: the question
in one sentence, the options, what turns on it, and a recommendation or an
explanation of why there isn't one.

## One checkout per seat

Give every seat that writes code its own git worktree, its own local database
and its own port. This is the single change that makes multi-agent work stop
being chaotic, and it costs one `git worktree add` per seat.

## Adopting it

```bash
git clone https://github.com/greentheo/the-firm
cp -r the-firm/firm your-project/firm
```

Then, in `your-project/firm/README.md`:

- `{project}` → your repo name, `{Project}` → your product name, `{owner}` →
  your GitHub owner.
- Fill in **rule 6**. Do not skip it and do not make it generic; it is the one
  that decides the close calls.
- Replace the worktree setup block — the example is Next.js and Postgres.
- Delete roles you will not staff. Four seats work; so do two. `EE.md` and
  `BENCH.md` are for hardware projects only.

Point each agent session at `firm/README.md` and name its role.

## Where it came from

Built for a real project and still running one: the live instantiation is
[`greentheo/langue`](https://github.com/greentheo/langue), where `firm/` is the
same five docs with the placeholders filled in. The rules were not designed up
front — each was written the day something went wrong that it would have
prevented.

The two hardware seats came later, from a tool that turns a circuit
description into a routed board and a parts order, after a review found that
every component fault in it had been caught by accident.

It assumes GitHub Issues, `gh`, git worktrees, and an agent runtime where
sessions can message each other. Nothing else.

## Licence

MIT. See [LICENSE](LICENSE).
