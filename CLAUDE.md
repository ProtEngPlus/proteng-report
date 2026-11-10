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
3. Read `project/overview.md`, then only the other source files the task needs
   (see section 2).

## 2. Where facts come from
Facts live in two places. Never invent facts that are in neither.

| Level | Folder | Holds |
|---|---|---|
| Project-wide | `project/` | `overview.md` (title, team, advisors, problem, users, objectives, scope, tech stack), `notes/` (meetings, research). Shared by every deliverable. |
| Per deliverable | `<deliverable>/docs/` | The official template and rules for that deliverable, its deadline, advisor/committee feedback on it. Used only by that deliverable. |

- Read order: `project/` first, then the current deliverable's `docs/`.
- If they conflict, `project/` wins; record the conflict in the deliverable's
  CONTEXT.md → Open questions and ask me.
- A deliverable never reads another deliverable's `docs/`. If something is
  needed by more than one deliverable, it belongs in `project/`.
- When the team changes a project-wide fact (e.g. an objective), update
  `project/overview.md` first, then every deliverable that uses it.
- All source files (`project/` and every `docs/`) are read-only unless I ask
  you to edit them. Exception: you may update `project/overview.md` when I
  confirm a new or changed fact.
- If a fact is missing, ask me or write `[TBD: what is missing]`.
- Follow the official template in the deliverable's `docs/` exactly:
  section order and page limits.

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
project/                 project-wide facts (shared by all deliverables)
  overview.md
  notes/
midterm_proposal/        Semester 1 proposal
  CONTEXT.md             outline, workflow, progress
  docs/                  template + feedback for this deliverable (read-only)
  sections/              one .md per template section
  figures/               diagram sources + exports
  references.md          reference list for this deliverable (IEEE)
```
Later deliverables (proposal slides, progress updates, final report, final
slides) get their own folder with the same pattern, including their own
`docs/`. One Markdown file per template section; do not merge sections.

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
  source files the facts came from, and any open citations or data.