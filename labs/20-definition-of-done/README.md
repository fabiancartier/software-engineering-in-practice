# Lab 20 — What does "done" mean?

## Story

Two teammates disagree. One says a task is done once the tests pass.
The other says it is not done until the change is merged and CI is
green. Each definition covers only part of the work.

## Learning objectives

After this lab you should be able to:

- Write a concrete, checkable Definition of Done for a project.
- Distinguish a checkable criterion ("CI is green") from a vague one
  ("the code is good").
- Evaluate completed work against a written standard rather than a
  feeling.

## Before you start

- Complete Labs 06-19 in your chosen track.
- You will not write code in this lab. Before starting, skim the
  "Acceptance criteria" sections of Labs 11 through 19. There is no
  need to recreate them.

## Your task

1. Write `labs/20-definition-of-done/definition-of-done.md` containing
   one Definition of Done checklist with 5-8 items. Use Markdown
   checkboxes (`- [ ] item`). The checklist should apply to *any* future
   change to `examples/team-inventory/<your-language>`. Build it from
   practices you used in Labs 06-19, such as tests, review, CI, and
   documentation. Keep it language-neutral and do not require a tool
   used by only one track.
2. Make every item checkable. A teammate should be able to answer yes
   or no by inspecting something concrete, such as a command's exit
   code, a file, or a pull request state. Avoid vague statements such
   as "the code is clean" or "it works well."
3. Pick one specific past lab (11 through 19) and, in the same file,
   check its actual outcome against your own checklist item by item.
   Where it doesn't fully satisfy an item, say so honestly.

## Acceptance criteria

- `definition-of-done.md` has between 5 and 8 checklist items, each
  independently checkable.
- Include the retrospective check against one named past lab, with an
  honest answer for every item rather than marking everything "yes" by
  default.

## Verification

```bash
test -f labs/20-definition-of-done/definition-of-done.md && echo "DoD exists"
grep -c '^- \[' labs/20-definition-of-done/definition-of-done.md
```

The file should exist, and the count should be between 5 and 8.

## Think about it

- Is a Definition of Done a technical concept or a team agreement?
  Could two teams working on similarly sized projects reasonably use
  different Definitions of Done?
- Which item on your list would have been impossible to write before
  Lab 19 existed? What does that tell you about how a Definition of
  Done evolves alongside a project's own tooling?

## If you get stuck

- **Hint 1.** Possible items include tests passing locally and in CI,
  a pull request description that explains the reason for the change,
  completed code review, resolved comments, no remaining conflict
  markers, and a merged change rather than merely an open pull request.
- **Hint 2.** If an item can't be checked by running a command or
  looking at a specific piece of state, rewrite it until it can.
- **Hint 3.** For the style of a good checklist or notes file, look back
  at Lab 12's `COMPARISON.md` or Lab 15's `my-notes.md`.

## What's next

Act IV is complete. You can work safely with a team by using branches,
resolving conflicts, reviewing code, and letting CI catch what people
miss. Next, the software has to face the outside world, including other
systems, stored data, and failures that are nobody's fault.

Continue to [Lab 21 — An API is a contract](../21-api-is-a-contract/README.md).
