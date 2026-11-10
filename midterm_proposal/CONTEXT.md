# CONTEXT.md — Midterm proposal (2110488, Semester 1)

Read this before working on the proposal. Update it at the end of every session.
General rules (where facts come from, style, figures, references) are in the root `CLAUDE.md`.
Facts: project-wide ones in `project/overview.md`; proposal-only ones in `midterm_proposal/docs/`.

## 1. What this deliverable is
The written CP Capstone Project Proposal, in English with a Thai บทคัดย่อ.
It is scored on problem definition, problem solving, design, teamwork, ethics,
and communication, and the project must pass it to continue to Semester 2.
The official template ("CP Capstone Project Proposal") is in `midterm_proposal/docs/`.

## 2. Outline and target length
Order must match the template exactly. One file per row in `sections/`.

| File | Template section | Target |
|---|---|---|
| 00-front-matter.md | Title page (Thai): Thai/English titles, team + student IDs, advisors, course, academic year | – |
| 00-abstract-th.md | บทคัดย่อ + คำสำคัญ 5 คำ | ~1/2 page |
| 00-abstract-en.md | Abstract + 5 keywords | ~1/2 page |
| 01-introduction.md | 1 Introduction & Problem Definition: 1.1 Background & Motivation, 1.2 Problem Statement, 1.3 Objectives, 1.4 Scope & Limitations, 1.5 Expected Benefits | 2–3 pages |
| 02-related-work.md | 2 Problem Solving & Related Work: 2.1 Review of Existing Knowledge, 2.2 Comparison with Existing Systems, 2.3 Gap Analysis, 2.4 Linkage to Project | 2–3 pages |
| 03-design.md | 3 Proposed Solution / Design Concept: 3.1 System Overview, 3.2 Components, 3.3 Constraints Considered, 3.4 Diagrams | 3–5 pages |
| 04-teamwork.md | 4 Teamwork Plan: 4.1 Roles and Responsibilities, 4.2 Collaborative Plan, 4.3 Task Coverage, 4.4 Workload Balance | 0.5 page |
| 05-timeline-sem1.md | 5 Timeline (Semester 1): table Week / Task / Deliverable | 1 page |
| 06-planned-work-sem2.md | 6 Planned Work (Semester 2) | 1 page |
| 07-ethics.md | 7 Ethics, Privacy, and Professional Considerations: 7.1 Plagiarism Check, 7.2 Credibility of Information, 7.3 Intellectual Property & Law | 0.5 page |
| ../references.md | References (IEEE) | – |
| 99-appendices.md | Optional: A Wireframes & Mockups, B Preliminary Designs & Diagrams, C Early Test Datasets, D Example Interfaces | – |

## 3. Workflow
Write in this order, because later sections depend on earlier ones:

1. **Collect facts.** Read `project/overview.md` and `midterm_proposal/docs/`.
   List every `[TBD]` that the proposal needs (see section 4) and ask me to
   fill the gaps before drafting. Confirmed project-wide facts go into
   `project/overview.md`, not into this file.
2. **Section 1 (Introduction).** It sets the problem and objectives that every
   other section refers to. 1.2 must say why the problem is complex and which
   ≥ 2 sub-disciplines it needs. 1.3 objectives must be measurable.
3. **Section 2 (Related Work).** Find real sources and add them to
   `references.md`. 2.2 works best as a comparison table plus a paragraph of
   analysis. 2.3–2.4 must link each gap to something in our design.
4. **Section 3 (Design).** Overview, components, constraints (performance,
   scalability, usability, sustainability), then diagrams in `figures/`.
5. **Sections 4–7.** Teamwork, timeline, Semester 2 plan, ethics. Roles and
   timeline must match the team in `project/overview.md`.
6. **Abstracts (TH + EN).** Written last, from the finished sections. Pattern:
   problem → importance → objectives → approach → components → tools/methods
   → expected deliverables for Sem 1 and 2 → benefits → 5 keywords.
7. **Review pass.** Check page counts, figure/table numbering and in-text
   references, every citation has an entry, consistent tense and terms,
   no `[TBD]` / `[CITATION NEEDED]` left.
8. **Export.** Combine sections in template order, convert citation tags to
   IEEE numbers, then paste into the Google Doc template. The Table of
   Contents and continuous page numbers are done in the Google Doc.
9. **Before submission.** Run Turnitin through the Capstone account.

Tense: present for the problem and the design, future for what will be built
("The system will…").

For each step: draft one section, stop, and wait for my review before moving on.

## 4. Facts this proposal needs
Project-wide facts are not copied here; read them from `project/overview.md`.
Before drafting, check that overview.md has: Thai + English title, team with
student IDs, main advisor (+ co-advisor), problem, target users, measurable
objectives, ≥ 2 sub-disciplines, scope, tech stack.

Proposal-only facts (source: `midterm_proposal/docs/`):
| Fact | Value | Source file |
|---|---|---|
| Submission format | Google Doc template → [TBD: PDF? upload where?] | |
| Advisor feedback received | (none yet) | |

### Official schedule (from the course site, all 2026, group submissions)
| Assignment | Opens | Due | What it is for us |
|---|---|---|---|
| Proposal Submission & Exam Schedule | Nov 9 | **Nov 29** | Submit the proposal report (this folder) [TBD: also pick an exam slot?] |
| Proposal Defense Presentation | Nov 16 | **Dec 6** | Thai slides + talk → separate deliverable folder, start after Nov 29 |
| Revised Proposal Report Submission | Nov 16 | **Dec 13** | Revise this report from committee feedback |
Due times: [TBD — check the clock icon on the course site].

### Working plan (adjust in "Next steps" if we fall behind)
| Dates | Goal |
|---|---|
| Nov 10–12 | Fill `project/overview.md`; resolve every [TBD] in section 4 |
| Nov 13–16 | Section 1 Introduction + Section 2 Related Work (find real sources) |
| Nov 17–21 | Section 3 Design + diagrams in `figures/` |
| Nov 22–24 | Sections 4–7 (Teamwork, Timeline, Sem 2 plan, Ethics) |
| Nov 25–26 | Thai + English abstracts; full review pass (step 7) |
| Nov 27–28 | Export to Google Doc, TOC + page numbers, Turnitin; buffer day |
| Nov 29 | **Submit** |
| Nov 30–Dec 6 | Defense slides + rehearsal (Thai) |
| Dec 7–13 | Apply committee feedback → record each change in section 6 Decisions → resubmit Dec 13 |

If a session ends behind this plan, say so in the session log and propose
what to cut or move.

## 5. Status
| Section | Status | Words | Notes |
|---|---|---|---|
| Front matter | Not started | – | |
| บทคัดย่อ / Abstract | Not started | – | Write last |
| 1 Introduction | Not started | – | |
| 2 Related Work | Not started | – | |
| 3 Design | Not started | – | |
| 4 Teamwork | Not started | – | |
| 5 Timeline (Sem 1) | Not started | – | |
| 6 Planned Work (Sem 2) | Not started | – | |
| 7 Ethics | Not started | – | |
| References | Not started | – | |
| Appendices | Optional | – | |

Status values: Not started / Drafting / Review / Done

## 6. Decisions (do not change without asking)
- Reference style: IEEE

## 7. Next steps
1. (by Nov 12) Read `project/overview.md` and `midterm_proposal/docs/`; check section 4.
2. (by Nov 12) Ask me about every `[TBD]`.
3. (Nov 13–16) Draft 1.1 Background & Motivation and 1.2 Problem Statement.

## 8. Open questions / [TBD] / [CITATION NEEDED]
- (none yet)

## 9. Session log (newest on top)
### YYYY-MM-DD — VS Code / iPad
- Did:
- Changed files:
- Left off at: