# Editorial style for course materials

This guide defines the voice used in the English and Polish course
materials. It applies to the root documentation, setup guides, and all
lab READMEs.

The goal is simple: the text should sound like an instructor explaining
an exercise to a student. It should be clear and precise without sounding
formal, promotional, or machine-translated.

## Write for the student

- Address the student directly.
- Prefer short sentences and common words.
- Give the instruction first. Add the reason only when it helps the
  student make a decision or recover from a mistake.
- State requirements plainly. Do not strengthen every requirement with
  words such as "real", "actual", "exactly", "genuine", or
  "explicitly".
- Avoid repetitive contrasts such as "not X, but Y" when a direct
  statement says the same thing.
- Use colons and parenthetical comments sparingly. A new sentence is
  often easier to read.

## Keep the two languages natural

The English and Polish versions must teach the same material, but they do
not need to mirror each sentence. Translate the meaning, not the syntax.

For Polish in particular:

- Prefer established Polish wording where it is clear: "wybrana ścieżka
  językowa", "narzędzie do testów", "funkcja systemu", "przegląd kodu".
- Keep common technical terms such as `commit`, `branch`, pull request,
  CI, API, and framework when replacing them would make the instruction
  less familiar to students.
- Avoid literal phrases such as "ścieżka domowa", "dotknąć pliku",
  "utrzymać zmianę w głowie", or "prawdziwy PR".
- Prefer forms that do not mark gender, for example "uruchom",
  "sprawdź", and "możesz". Use forms such as "zrobiłeś/aś" only when a
  natural neutral construction is not available.
- Do not use a spaced slash to join alternatives in prose. Write a list
  with commas and a conjunction instead.

## Punctuation

- Use a dash only when it improves the sentence. Do not rely on dashes
  for routine explanations, qualifications, or transitions.
- Prefer a full stop when two thoughts can stand on their own.
- Keep arrows in process diagrams and menu paths, where they convey
  structure rather than sentence rhythm.
- Do not mechanically replace punctuation across the repository. Rewrite
  the sentence if its structure is the problem.

## Preserve the technical contract

An editorial change must not alter:

- commands, source code, identifiers, paths, versions, or expected output;
- the required task, acceptance criteria, or verification procedure;
- the supported Python, Go, and Java paths;
- links between labs or the order of the course.

Executable code blocks must remain byte-identical between the English and
Polish versions. `python3 scripts/check_course_structure.py` checks this
invariant.

## Review checklist

Before committing an editorial change, read it aloud and check:

1. Would an instructor naturally say this to a student?
2. Can a long sentence become two shorter ones?
3. Does an intensifier add information, or only emphasis?
4. Does the Polish version stand on its own without knowledge of the
   English sentence?
5. Did any command, requirement, expected value, or link change by
   accident?
6. Do `git diff --check` and the course structure checks pass?
