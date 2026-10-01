# Decision log

One entry per decision that shapes the project. Newest first. Each entry: date, decision, reason, what it affects.
A dropped step or a changed quota goes here, not only in TODO.md.

## 09/30/2026 — Residency: split DACA from undocumented
**Decision:** `residency` gets a new value, `undocumented eligible` (undocumented students explicitly included, with or without DACA, or AB 540 students named). `DACA eligible` now means DACA recipients only.
**Reason:** DACA is what separates some undocumented students from others: a DACA-only award excludes undocumented students without DACA, while an undocumented-eligible award includes them. One merged value would hide that eligibility flip, which the error analysis is meant to catch. Listings use "AB 540" to mean undocumented students who attended California schools, so the guide maps it to `undocumented eligible` rather than leaving it to each labeler.
**Affects:** docs/schema.md (version 0.2), docs/labeling-guide.md (Rule 12, version 0.4), eval/score.py (allowed values). No existing record used `DACA eligible`; no relabeling.

## 09/30/2026 — Exact quoting only in `award_amount`; short phrases in `other_requirements`
**Decision:** Rule 3 (quote, do not paraphrase) applies to `award_amount` only. `other_requirements` uses short phrases in the page's key words, with numbers, dates, names, and qualifiers copied exactly (Rule 19).
**Reason:** Rules 3 and 19 contradicted each other. `award_amount` is scored, so exact wording matters; `other_requirements` is not scored and is used to scan and group requirements, where full quotes become unwieldy. Meaning drifts mainly through numbers and qualifiers, so those must be exact.
**Affects:** docs/labeling-guide.md (Rules 3 and 19, version 0.3). Existing records already follow it; no relabeling.

## 09/30/2026 — Shared page text applies to every award (Rule 2b)
**Decision:** Text on a listing page that applies to all its awards (shared requirements, deadlines, default GPA) goes into every record from that page; an award's own text overrides it. Menus, sidebars, linked pages, and general office text do not count.
**Reason:** A student reads shared requirements as applying to each award, and the model sees the same page, so gold records that drop shared text would mark correct model answers wrong. Two pages already need this (CSUSB Music, CSUSB World Languages), and vendor portals use the same pattern. The same-page limit keeps labelers from pulling in text from other pages.
**Affects:** docs/labeling-guide.md (Rule 2b, version 0.2), data/labeled/listings.jsonl (csusb-music records), Step 6 input design (rule holds whether the model gets the whole page or one award at a time).

## 09/30/2026 — All majors, not STEM only
**Decision:** Listings span all colleges; aim for no more than 40 percent STEM-specific majors, and include business, education, arts and humanities, social sciences, health, and any-major awards.
**Reason:** The research question is about listing formats, and a STEM-only set would limit what the results say. Any-major and humanities listings also tend to be the sparse, prose-style hard cases.
**Affects:** PROPOSAL sections 3 and 6, TODO Steps 3, 4, 7, and 8.

## 09/30/2026 — Open-source only: Apache-2.0 models, no API models
**Decision:** Open-source only: Apache-2.0 models (Qwen 2.5, Mistral, OLMo 2); no API models; Llama excluded because of its license.
**Reason:** Apache-2.0 models can be run locally and reproduced by anyone without usage terms or cost, and results will not change when a vendor updates a hosted model. Llama's community license adds use restrictions, so it does not count as open-source here.
**Affects:** PROPOSAL section 8 (models evaluated), README.

## 09/30/2026 — Schema v0.1: added `financial_need` and `other_requirements`
**Decision:** Two fields added to the labeled schema. `financial_need` (required / not stated) is scored; `other_requirements` (free list) is not scored in v0.1.
**Reason:** The first worked example (CSUSB ExCELS) states financial need as a hard requirement and has several eligibility statements (full-time enrollment, review date) that fit no field. Need is common in scholarships and changes eligibility, so it is scored; the catch-all keeps the schema small while preserving text for error analysis.
**Affects:** docs/schema.md, docs/labeling-guide.md, eval/score.py.

## 09/30/2026 — "Reviews begin" is not a deadline
**Decision:** Only a submission cutoff counts as `deadline`; review dates, priority dates, and open dates go to `other_requirements`.
**Reason:** The first example would otherwise have been labeled with a date that is not a deadline, which is exactly the kind of error the benchmark should catch in models, so the gold must not make it.
**Affects:** Labeling guide Rule 14.

## 09/29/2026 — Agreement: second labeler preferred, self re-labeling as fallback
**Decision:** Aim for a second person labeling ~100 items; if unavailable, re-label 20-30 items after two weeks and state it as a limitation.
**Reason:** Recent benchmarks (SkillSpan, Chinese-SkillSpan) measure agreement on a ~100-item subset with a second annotator; no paper found endorses self-agreement, so it must be labeled as weaker.
**Affects:** TODO Step 9, PROPOSAL section 8.

## 09/29/2026 — Quotas by format, not by campus
**Decision:** 70 portal / 70 prose / 40 PDF-flyer-email / 20 research or system-wide; at least 5 campuses; no single source over ~50.
**Reason:** The research question is about formats. CSUSB's own portal is login-only (~40 public items), while SDSU has ~800, so per-campus quotas would either starve the set or force scraping behind logins. Portal-heavy sets would also be too easy.
**Affects:** PROPOSAL section 6, TODO Steps 4 and 8.

## 09/29/2026 — Stage 1 is the benchmark only
**Decision:** No app, agent, recommender, user study, or model training in Stage 1.
**Reason:** A benchmark is the measuring stick every later stage needs, is the smallest scope that stands alone, and fits 2-4 hours/week alongside the capstone and coursework.
**Affects:** PROPOSAL section 5 and 12.

## 09/29/2026 — Independent of the 2025 lab proposal
**Decision:** Build from this repo's documents and new research only; do not reuse the text of the Spring 2025 OpportunityScout proposal.
**Reason:** That proposal is under the lab's ownership policy. This project asks a different question (measurement, not recommendation), uses different sources and methods, and is done outside the lab. Credit the origin; reuse nothing.
**Affects:** All documents; README "Relationship to prior work".

## 09/29/2026 — Gap confirmed
**Decision:** Proceed to Step 2.
**Reason:** No public benchmark for eligibility extraction from student-opportunity listings was found (web search plus full reads of Padiya et al. 2024 and Figueroa-Gómez & Galpin 2025). Closest work is institution-side classification on private data. Pending: a Google Scholar pass.
**Affects:** TODO Step 1.
