# CLAUDE.md — CP Capstone Project (2110488 / 2110489)

This repo holds the written deliverables of our CP Capstone Project
(Computer Engineering, Chulalongkorn University). Content is drafted here in
Markdown, then moved into the official Google Doc / slide templates.

These rules apply to every deliverable. Each deliverable has its own folder
with a `CONTEXT.md` that explains its outline, workflow, and current progress.

## 1. Start of every session
1. Ask which deliverable we are working on if it is not clear.
2. Read that folder's `CONTEXT.md` (e.g. `midterm_proposal/CONTEXT.md`)
   and continue from its "Next steps". Do not redo work marked Done unless asked.
3. Read only the files in `docs/` that the task needs.

## 2. Facts come from `docs/`
`docs/` holds the source documents: course guideline and templates, project
idea and requirements, meeting notes, advisor feedback, research material.

- Take every project fact (title, team, problem, objectives, scope, users,
  tech stack, timeline) from `docs/`. Never invent facts that are not there.
- `docs/` is read-only. Do not edit, move, or delete files there unless asked.
- If two docs disagree, prefer the newest one and record the conflict in the
  deliverable's CONTEXT.md → Open questions.
- If a fact is missing, ask me or write `[TBD: what is missing]`.
- The official guideline and report templates are in `docs/`. Follow their
  section order and page limits exactly.

## 3. End of every session (or when I say "wrap up")
1. Update the deliverable's `CONTEXT.md`: status table, decisions,
   next steps, open questions, session log.
2. `git add -A && git commit -m "<folder>: <what changed>" && git push`

I switch between VS Code (laptop) and Claude Code on the web (iPad).
Anything not written to a file in this repo is lost, so record decisions in
CONTEXT.md, not only in chat.

## 4. Repo layout
```
CLAUDE.md                general rules (this file)
docs/                    source documents = facts (read-only)
midterm_proposal/        Semester 1 proposal
  CONTEXT.md             outline, workflow, progress
  sections/              one .md per template section
  figures/               diagram sources + exports
  references.md          reference list for this deliverable (IEEE)
```
Later deliverables (proposal slides, progress updates, final report, final
slides) get their own folder with the same pattern. One Markdown file per
template section; do not merge sections.

## 5. Writing style (written reports)
- Formal academic English: clear, concise, grammatically correct.
- Paragraph-led writing. No bullet-only sections and no sentence fragments
  (tables and short lists inside a paragraph-led section are fine).
- Keep one tense style per deliverable (see its CONTEXT.md).
- The Thai บทคัดย่อ must say the same thing as the English Abstract; both end
  with 5 keywords. Write abstracts last.
- Objectives must be measurable and stay the same across deliverables.
- Never invent statistics, results, or citations. If evidence is missing,
  write `[CITATION NEEDED: what to find]` or `[DATA NEEDED: what to measure]`
  and add it to Open questions. Falsified data means failing the course.
- Rough length guide: 1 page ≈ 400–450 English words of body text.

## 6. Figures and tables
- Every figure has a number, title, and caption, e.g. `Figure 1: System Architecture`.
- Every table has `Table 1: ...` above it.
- Every figure and table is referred to in the text ("as shown in Figure 1").
- Number in order of first mention, restarting per deliverable.
- Labels must be readable when printed. Keep editable sources.

## 7. References
- IEEE style for every deliverable; never mix with APA.
- In drafts, cite with a tag such as `[@smith2021]`; keep full entries in the
  deliverable's `references.md`. At export, convert tags to [1], [2]… in
  order of first citation.
- Every citation has an entry; every entry is cited at least once.
- Entries are complete: authors, title, venue/publisher, year; web pages also
  need the URL and "[Accessed: Mon. DD, YYYY]".
- Cite figures, diagrams, and data taken from others. Direct quotes need
  quotation marks and a citation; prefer paraphrasing.

## 8. What examiners look for
- A real-world, non-trivial problem that integrates ≥ 2 sub-disciplines of
  computer engineering.
- Design in Semester 1 and implementation in Semester 2 (both mandatory).
- Clear roles, full task coverage, fair workload.
- Awareness of risks, costs, resources, environmental impact, and societal
  responsibility (safety, inclusivity, public benefit).
- Ethics: Turnitin check, credible sources, IP and law
  (Thai personal data → PDPA B.E. 2562 where applicable).
- Scores come mainly from the written documents and slides.

## 9. How to help me
- Work on one section (or subsection) at a time, then stop for my review.
- When I give rough Thai notes, turn them into academic English and keep my
  meaning; ask when something is ambiguous instead of guessing.
- After drafting, report: word count, estimated pages vs target, which
  docs/ files the facts came from, and any open citations or data.