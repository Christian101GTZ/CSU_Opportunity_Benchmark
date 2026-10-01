# Decision log

One entry per decision that shapes the project. Newest first. Each entry: date, decision, reason, what it affects.
A dropped step or a changed quota goes here, not only in TODO.md.

## 2026-09-30 — Schema v0.1: added `financial_need` and `other_requirements`
**Decision:** Two fields added to the labeled schema. `financial_need` (required / not stated) is scored; `other_requirements` (free list) is not scored in v0.1.
**Reason:** The first worked example (CSUSB ExCELS) states financial need as a hard requirement and has several eligibility statements (full-time enrollment, review date) that fit no field. Need is common in scholarships and changes eligibility, so it is scored; the catch-all keeps the schema small while preserving text for error analysis.
**Affects:** docs/schema.md, docs/labeling-guide.md, eval/score.py.

## 2026-09-30 — "Reviews begin" is not a deadline
**Decision:** Only a submission cutoff counts as `deadline`; review dates, priority dates, and open dates go to `other_requirements`.
**Reason:** The first example would otherwise have been labeled with a date that is not a deadline, which is exactly the kind of error the benchmark should catch in models, so the gold must not make it.
**Affects:** Labeling guide Rule 14.

## 2026-09-29 — Agreement: second labeler preferred, self re-labeling as fallback
**Decision:** Aim for a second person labeling ~100 items; if unavailable, re-label 20-30 items after two weeks and state it as a limitation.
**Reason:** Recent benchmarks (SkillSpan, Chinese-SkillSpan) measure agreement on a ~100-item subset with a second annotator; no paper found endorses self-agreement, so it must be labeled as weaker.
**Affects:** TODO Step 9, PROPOSAL section 8.

## 2026-09-29 — Quotas by format, not by campus
**Decision:** 70 portal / 70 prose / 40 PDF-flyer-email / 20 research or system-wide; at least 5 campuses; no single source over ~50.
**Reason:** The research question is about formats. CSUSB's own portal is login-only (~40 public items), while SDSU has ~800, so per-campus quotas would either starve the set or force scraping behind logins. Portal-heavy sets would also be too easy.
**Affects:** PROPOSAL section 6, TODO Steps 4 and 8.

## 2026-09-29 — Stage 1 is the benchmark only
**Decision:** No app, agent, recommender, user study, or model training in Stage 1.
**Reason:** A benchmark is the measuring stick every later stage needs, is the smallest scope that stands alone, and fits 2-4 hours/week alongside the capstone and coursework.
**Affects:** PROPOSAL section 5 and 12.

## 2026-09-29 — Independent of the 2025 lab proposal
**Decision:** Build from this repo's documents and new research only; do not reuse the text of the Spring 2025 OpportunityScout proposal.
**Reason:** That proposal is under the lab's ownership policy. This project asks a different question (measurement, not recommendation), uses different sources and methods, and is done outside the lab. Credit the origin; reuse nothing.
**Affects:** All documents; README "Relationship to prior work".

## 2026-09-29 — Gap confirmed
**Decision:** Proceed to Step 2.
**Reason:** No public benchmark for eligibility extraction from student-opportunity listings was found (web search plus full reads of Padiya et al. 2024 and Figueroa-Gómez & Galpin 2025). Closest work is institution-side classification on private data. Pending: a Google Scholar pass.
**Affects:** TODO Step 1.
