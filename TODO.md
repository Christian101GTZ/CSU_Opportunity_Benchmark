# TODO

Stage 1 work plan, in order. One step at a time. Each step has a "Done when" line; tick the box only when it is true.
When a step changes the plan, record why in `docs/DECISIONS.md`.

Legend: `[ ]` not started · `[~]` in progress · `[x]` done · `[-]` dropped (say why in DECISIONS.md)

## Step 1. Confirm the gap
- [x] Read Padiya et al. (AITA 2024) and Figueroa-Gómez & Galpin (SN Computer Science 2025)
- [x] Web search for 2025-2026 work on scholarship / opportunity eligibility extraction
- [ ] Ten-minute Google Scholar pass with: "scholarship eligibility extraction", "opportunity listing extraction language model"
- [x] Three-sentence gap statement written into PROPOSAL.md section 2

**Done when:** the gap statement is in the proposal and the Scholar pass found nothing new. (09/29/2026: web check done; Scholar pass pending)

## Step 2. Set up the repo
- [ ] Create public GitHub repo `csu-opportunity-benchmark`
- [ ] Add folders: `data/raw/`, `data/labeled/`, `docs/`, `eval/`
- [ ] Add `README.md`, `PROPOSAL.md`, `TODO.md`, `docs/DECISIONS.md`, `LICENSE` (MIT), `data/LICENSE` (CC BY 4.0), `.gitignore`, `requirements.txt`
- [ ] Add LICENSE (MIT) at root and data/LICENSE (CC BY 4.0)
- [ ] Open in VS Code; first commit: "Initial layout and proposal"

**Done when:** the README is pushed and the folders exist.

## Step 3. Schema and labeling guide
- [ ] `docs/schema.md`: the fields from PROPOSAL.md section 7, with the scoring metric declared per field
- [ ] `docs/labeling-guide.md`: the rules, one decision per rule, plus one fully worked example
- [ ] `docs/hard-cases.md`: empty file with a header, to be filled while labeling
- [ ] Test the guide on a non-STEM listing (the worked example so far, CSUSB ExCELS, is STEM)

**Done when:** a classmate could label a listing using only the guide.

## Step 4. Collect the first 50 listings
- [ ] 20 portal listings (SDSU AcademicWorks, CSU Fullerton NGWeb)
- [ ] 20 department prose pages, beyond CNS and CSE: CSUSB Arts and Letters, Business, Education, CSBS; Cal Poly Pomona non-STEM departments
- [ ] Listings span all colleges, not only STEM: aim for no more than 40 percent STEM-specific majors, and include business, education, arts and humanities, social sciences, health, and any-major awards
- [ ] 10 PDFs or flyers
- [ ] For each: save raw text or PDF in `data/raw/<id>.*`; add a row to `data/sources.csv` with id, source_url, campus, format, collected_on
- [ ] Check robots.txt for every domain used; note the result in `docs/sources.md`

**Done when:** 50 rows in `data/sources.csv`, each with a saved raw file.

## Step 5. Label the 50
- [ ] Fill every schema field in `data/labeled/listings.jsonl`
- [ ] Log anything the guide did not cover in `docs/hard-cases.md`
- [ ] Add a rule to the guide when a hard case recurs; note the change in DECISIONS.md

**Done when:** 50 labeled records and an updated guide.

## Step 6. Run one model
- [ ] Install Ollama; pull one 7B-8B model
- [ ] `eval/extract.py`: sends raw text + schema, returns JSON, saves to `eval/outputs/<model>/<id>.json`
- [ ] `eval/score.py`: compares outputs to gold per field; writes `eval/results/<model>.csv`
- [ ] `eval/test_score.py`: pytest for the scorer (exact, normalized, partial match)
- [ ] Decide whether the `majors` scorer strips "Department of" / "School of" so department names written differently still match
- [ ] Read every error; tag each with a cause

**Done when:** a per-field score table for 50 items and a list of error causes.

## Step 7. Decide
- [ ] Review errors and hard cases: is the schema right? are the quotas realistic?
- [ ] Count `majors` by college across the labeled set; if STEM-specific is over 40 percent, shift the next collection toward other colleges
- [ ] Write the decision (continue / adjust / pause) and the reason at the top of README.md and in DECISIONS.md
- [ ] Optional: present the 50-item results to Dr. Alzahrani

**Done when:** the decision is written down.

## Step 8. Reach ~200
- [ ] 70 portal · 70 prose · 40 PDF/flyer/email · 20 research/system-wide
- [ ] At least 5 campuses; no single source over ~50
- [ ] Listings span all colleges; no more than 40 percent STEM-specific majors (check the Step 7 count)

**Done when:** about 200 labeled records meeting the quotas.

## Step 9. Agreement check
- [ ] Preferred: one other person labels ~100 items using only the guide
- [ ] Fallback: re-label 20-30 items yourself after a two-week gap (state as a limitation)
- [ ] Compute agreement per field (Krippendorff's alpha for closed fields, pairwise F1 for free text)
- [ ] Fix the guide where you disagreed; record changes in DECISIONS.md

**Done when:** an agreement number per field is in the proposal and README.

## Step 10. Full baselines
- [ ] Two or three open models via Ollama + regex baseline (+ one API model if budget allows)
- [ ] Report per field, per format, whole-record accuracy, valid-JSON rate
- [ ] Error taxonomy with counts and examples
- [ ] Eligibility-flip check on 20 sample student profiles

**Done when:** results tables and the error taxonomy exist in `eval/results/`.

## Step 11. Write it up
- [ ] Update PROPOSAL.md sections with real numbers; add a Results section
- [ ] Fill `docs/DATASET_CARD.md` (Hugging Face template)
- [ ] Reproducibility check: fresh clone, `pip install -r requirements.txt`, run `eval/score.py`, same numbers

**Done when:** someone else can clone the repo and get the same scores.

## Step 12. Share and decide on Stage 2
- [ ] Send the write-up to Dr. Alzahrani
- [ ] Publish the dataset (GitHub release + Hugging Face)
- [ ] Decide whether to build the app (Stage 2); record in DECISIONS.md

**Done when:** the dataset is public and the next stage is chosen.
