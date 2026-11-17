# The bench

*An optional seat, for projects where the output gets built. It is the only
seat that is not an agent.*

**The bench turns a procedure with predicted values into measured values in
the repo.** It is whoever holds the meter, which is usually the owner. This
file is the protocol between the crew and that person.

## Why it is written down

Every number an agent produces about hardware comes from a model. No agent can
measure anything. Until a measurement is recorded, a design is "simulated",
whatever colour the report is. If the hand-off to a person with a meter is not
planned, it happens late, informally, and only for the parts that visibly
misbehave.

## What the crew gives the bench

A procedure, written by the [EE](EE.md) and reviewed like any pull request. It
must be followable without the conversation around it:

- **What you need**: instruments, supply, the exact parts.
- **Before power**: orientation of every polarised part, and what to leave out
  for the first stage.
- **Stages**, ordered so a fault shows before it can damage the next stage:
  power alone first, with the expensive parts out. For each step:

  | Step | Measure | Between | Predicted | Tolerance | From | If it misses |
  |---|---|---|---|---|---|---|
  | 1.1 | rail voltage | supply rail and ground | 11.6 V | ±0.2 V | which simulation or calculation | stop; check the input diode's orientation |

- **Refusal cases**: what the circuit must not do, and how to provoke each.
- **Stop conditions**: anything hot, any smell, any current above a stated
  figure. Stop means remove power.

A step never says "check it works". It names a quantity and a number.

## What the bench gives back

A dated results file in the repo:

- the date, the procedure's commit, the instruments used
- the parts actually fitted, where they differ from the parts list
- the same table with a **Measured** column, every step filled or marked
  skipped
- anything noticed that the procedure did not ask about

The owner can dictate this to any seat. That seat writes the file and says in
it that the values are as reported by the owner.

## What happens next

The EE compares measured with predicted. Within tolerance: the model behind
that prediction is marked as confirmed by measurement, with the date. Outside
tolerance: an issue saying which is wrong, the circuit or the model. A
disagreement is never a footnote.

Issues labelled `bench` close only when their results file is merged. Nothing
else closes them: not a simulation, not a review, not a passing build.

## Limits

- No agent tells the owner that something is safe to power. The procedure
  states what was reviewed and what the stop conditions are.
- Anything on mains voltage, or a battery that can start a fire, has its
  procedure read by the owner before the first step, not after.
