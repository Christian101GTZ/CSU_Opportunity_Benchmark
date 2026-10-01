# CSU Opportunity Extraction Benchmark: Research Proposal

**Author:** Christian A. Gomez Diaz (CSUSB, B.S. Computer Science, expected May 2027)
**Date:** 2026-09-29
**Status:** Stage 1, independent work

Stage 1 builds a public, human-labeled dataset of about 200 CSU-system opportunity listings in mixed formats and measures how reliably open-weight language models extract their eligibility fields. Written in the structure of the CSUSB AI Research Lab proposal template; the content is new and independent.

---

## 1. Problem statement

Undergraduate opportunities (scholarships, fellowships, research programs) across the CSU system are published in inconsistent formats, and no public dataset exists to test whether language models can turn them into structured eligibility records.

Listings live on vendor portals, department prose pages, PDFs, flyers, and emails. Eligibility is often implied rather than stated, deadlines are missing or conflicting, and one page can describe several awards. Any tool that wants to match students to opportunities must first extract these fields correctly, and today there is no way to measure how well a model does that.

**Research question:** How reliably can open-weight large language models extract eligibility fields from real CSU opportunity listings, and which fields and formats fail?

## 2. Background and related work

A literature check on 2026-09-29 found no published benchmark for extracting eligibility fields from scholarship or research-opportunity listings. The closest work falls into four groups.

| Area | Representative work | What it does | Gap it leaves |
| --- | --- | --- | --- |
| Scholarship ML | ScholarSpot (IEEE ICCUBEA 2024); Padiya et al., Enhancing Scholarship Opportunities: A Multi-label Classification Approach (AITA 2024, Springer 2025) | Classical classifiers (SVM, random forest) predict scholarship fit; multi-label classification on dummy students and scholarships | Institution-side or category-level; no field extraction, no real labeled listings |
| Funding calls | Figueroa-Gómez and Galpin, From Text to Decision (SN Computer Science 2025) | GPT-4o-mini classifies 100 private funding-call PDFs as relevant or not for one institution (F1 0.84) | Binary classification on a private set; not per-field extraction, not student-facing; authors recommend expanding to multiple institutions |
| Job postings | SkillSpan / Skill-LLM (AAAI 2025); Feature extraction with LLM finetuning (arXiv 2501.07663) | Span-level skill extraction; extraction of pay, experience, and education requirements from postings | Schema is skills or job requirements, not student eligibility; labeled data not released |
| General extraction | ExtractBench (arXiv 2602.12247); LLMStructBench (arXiv 2602.14743) | Per-field scoring of JSON extraction from PDFs and text, finance and legal domains | No education domain, no eligibility semantics; their per-field scoring method is reusable here |

Adjacent evidence that the pattern works: clinical-trial eligibility extraction (Criteria2Query 3.0, 2024; EC-RAFT, ACL Findings 2025) is a mature research line in medicine that has not been ported to education.

**Gap check (Step 1, done 2026-09-29).** What exists: classical and multi-label classifiers that recommend scholarships from synthetic or private data, one institution-side LLM classifier of 100 private funding-call PDFs, mature job-posting extractors with a skills schema, and generic per-field extraction benchmarks in finance and legal domains. What does not exist: any public, human-labeled dataset of real student-opportunity listings with per-field eligibility labels, or any per-field LLM extraction results on such text; the only student-facing agents found (a Kaggle capstone demo, OpportunityX on GitHub) publish no data or evaluation. Why it is still worth doing: every matching tool or agent for students depends on this extraction step, and today there is no way to measure it.

## 3. Novelty and contribution

The contribution is a domain-specific benchmark, not another generic JSON-extraction test.

- **A public, human-labeled dataset** of about 200 real CSU-system listings, in mixed formats, with source URL and collection date on every item. None exists today.
- **An eligibility schema** built for student opportunities: major, GPA, class year, residency or citizenship, deadline, award amount, campus, and scope (campus-only, system-wide, or open).
- **Hard cases on purpose:** implicit eligibility, missing or conflicting deadlines, multi-award pages, and sparse department prose, which generic benchmarks do not cover.
- **A downstream check:** whether each extraction error would change a student's eligibility decision, which links extraction quality to real consequences.
- **Baseline results** for open-weight models, so later work (including an agent) has something to be measured against.

## 4. Research objectives

1. **Dataset.** Collect and label about 200 public CSU listings meeting the format quotas in section 6, from at least 5 campuses. Measurable outcome: the dataset, with every item carrying its source URL, collection date, and all schema fields.
2. **Labeling quality.** Write a one-page labeling guide with one decision per rule, and have a second labeler label about 100 items (fallback: re-label 20 to 30 items yourself after two weeks, stated as a limitation). Measurable outcome: a reported agreement number per field.
3. **Baselines.** Run three Apache-2.0 models (Qwen 2.5 7B, Mistral 7B, OLMo 2 7B) via Ollama with the same prompt and output schema. Measurable outcome: per-field exact-match and partial-match scores, plus overall record accuracy.
4. **Error analysis.** Classify every error by field and by source format, and flag which errors would flip an eligibility decision for a sample student profile. Measurable outcome: an error taxonomy with counts and examples.

## 5. Scope boundaries

Stage 1 stops at the benchmark. It does not include:

- a user study or student survey
- a recommender or matching system
- an AI agent that collects listings on its own
- training or fine-tuning a model
- an app or website
- scraping behind logins, against robots.txt, or against a site's terms of use

Each of these is a later stage (section 12) and starts only after Stage 1 is complete.

## 6. Data plan

Quotas are set by format and type, not by campus, because the research question is about formats. Campus is recorded on every item so per-campus results can still be reported.

| Format / type | Target count | Candidate sources (checked 2026-09-29) | Notes |
| --- | --- | --- | --- |
| Vendor portal listings | 70 | SDSU AcademicWorks (~800 public); CSU Fullerton NGWeb (522 public); CSULB AcademicWorks (~100) | About 25 each; two different vendors give two templates |
| Department prose pages | 70 | CSUSB CNS (~24); CSUSB CSBS (~8); CSUSB CSE ExCELS (1); Cal Poly Pomona department pages (9+ per dept); two or three more campuses | The hard cases: sparse fields, implied eligibility |
| PDFs, flyers, emails | 40 | Department PDFs, posted flyers, forwarded campus emails | Collected by hand; record where each came from |
| Research and system-wide programs | 20 | CSUSB U-RISE / MARC; Sally Casanova (via a campus mirror); NSF REU site pages opened manually; HSF | Adds citizenship and class-year variety |

Collection rules:

- Cap any single source at about 50 items so no portal dominates.
- Include at least 5 campuses.
- Collect by hand or with light tooling on public pages only; respect robots.txt (the NSF REU search page disallows crawling, so open individual REU sites instead) and vendor terms.
- Save the raw text or PDF, the source URL, and the collection date for every item. The dataset is a frozen snapshot; it is not meant to stay current.
- CSUSB's own scholarship portal is behind MyCoyote and is out of scope. Cal Poly Pomona's portal reopens 2026-10-01.

Open question: how many PDFs and flyers are reachable without a login. If fewer than 40, lower that quota and say so in the write-up.

## 7. Schema and labeling

Every listing gets one record with these fields. A field the listing does not state is labeled `not stated`, never guessed.

| Field | Type | Example | Rule |
| --- | --- | --- | --- |
| campus | text | CSUSB | The campus that publishes it |
| opportunity_type | one of: scholarship, fellowship, research program, internship, other | scholarship | |
| majors | list of text | Computer Science; Computer Engineering | As written; `any` if open to all majors |
| min_gpa | number or not stated | 3.0 | Only if a number is given |
| class_year | list of: freshman, sophomore, junior, senior, graduate | junior; senior | Map phrases like "upper division" to junior; senior |
| residency | one of: US citizen or permanent resident, DACA eligible, California resident, any, not stated | US citizen or permanent resident | As written |
| financial_need | one of: required, not stated | required | |
| deadline | date or rolling or not stated | 2026-02-13 | Convert to ISO date; `rolling` if the page says so |
| award_amount | text or not stated | $500 to $5,000 | Keep the range as written |
| scope | one of: campus-only, system-wide, open | campus-only | Who can apply, not who publishes it |
| other_requirements | list of short strings, or `[]` | `["full-time enrollment (12+ units)"]` | Not scored in v0.1; kept for error analysis |
| source_url, collected_on, format | text, date, one of: portal, prose, pdf, flyer, email | | Provenance |

Labeling guide rules (draft; each decides one thing):

1. Label only what the text states. Inference is an error.
2. A page describing several awards becomes several records, one per award.
3. If two deadlines conflict, label `conflict` and quote both in a note.
4. `scope` is decided by who may apply, never by which site hosts the listing.
5. Hard cases go in a running list with the reason, to become the error-analysis examples.

## 8. Methodology

**Models.** Three open-source (Apache-2.0) models run locally through Ollama: Qwen 2.5 7B, Mistral 7B, and OLMo 2 7B. Same prompt, same JSON output schema, temperature 0. One rule-based baseline (regex for GPA, dates, and dollar amounts) shows what the models add.

**Scoring.** Per field: exact match for closed fields (type, residency, scope, class year), normalized match for dates and GPA, and token-overlap partial credit for free-text fields (majors, amount). Also report the valid-JSON rate. Whole-record accuracy is the share of listings with every field correct. Report by field and by source format. The per-field method follows ExtractBench.

**Agreement.** Preferred: a second person labels about 100 items using only the guide, and agreement is reported per field (Krippendorff's alpha for closed fields, pairwise F1 for free-text fields). Fallback: the author re-labels 20 to 30 items after a two-week gap; this is weaker and is stated as a limitation. Disagreements are resolved by the guide, and the guide is updated when a rule was missing.

**Error analysis.** Each model error is tagged with a cause: not stated in text, stated but missed, wrong value, hallucinated value, format parsing failure, multi-award confusion. A sample of 20 student profiles (major, GPA, year, residency) is checked against gold and model records to count how many errors would flip an eligibility decision.

**Tooling.** Python, pandas, pytest for the scorer, a single CSV or JSONL for the dataset, everything in a public GitHub repo with a README that lets a stranger re-run the evaluation.

## 8b. How benchmark papers are normally built (2023-2026)

Checked 2026-09-29 against four recent extraction benchmarks and the standard checklists. This project follows the same pattern at a smaller scale.

| Practice | What recent papers do | This project |
| --- | --- | --- |
| Size | 35 PDFs / 12,867 fields (ExtractBench 2026); 995 synthetic emails (LLMStructBench 2026); 14,538 sentences (SkillSpan) | About 200 real listings, roughly 1,800 labeled fields |
| Sourcing | Public-domain or open documents; PII avoided | Public CSU pages and PDFs; no student data |
| Labeling guide | Written for ambiguous cases; released by only about a third of NLP papers | Written before labeling and released in the repo |
| Agreement | Often skipped even by industry teams (ExtractBench says so outright); when done, measured on a subset of about 100 items | A second labeler on about 100 items if possible; self re-labeling is the fallback and is stated as a limitation |
| Agreement metric | Fleiss or Cohen kappa for categories; pairwise F1 for spans; Krippendorff's alpha when fields can be missing | Krippendorff's alpha for closed fields, pairwise F1 for free text |
| Split | Test-only is common for LLM benchmarks | Test-only; no training |
| Metrics | Per-field metric declared per field (exact, fuzzy, numeric tolerance); valid-JSON rate; aggregate score | Same, declared in the schema file |
| Baselines | Several open models across sizes plus one API model; a rule-based baseline | Three Apache-2.0 models via Ollama and a regex baseline |
| Error analysis | A failure-mode taxonomy with counts | Same, plus the eligibility-flip check |
| Release | JSONL on Hugging Face or GitHub, a dataset card, an explicit license (CC BY for data, MIT for code), a scorer script | JSONL on GitHub and Hugging Face, dataset card from the Hugging Face template, CC BY 4.0 data, MIT code |

Checklist items reviewers expect (NeurIPS Datasets and Benchmarks, ACL Responsible NLP, Datasheets for Datasets): data and code public, license stated, intended and out-of-scope uses, collection and labeling process described, annotator information (here: one student, self-labeled, no pay or IRB, stated as such), limitations, and AI-assistance disclosure. All of these go in the README and dataset card.

## 9. Feasibility and timeline

This runs alongside the CodePath AI 301 capstone, coursework, and work, at about 2 to 4 hours per week. No deadline is set; the checkpoints decide whether to continue.

| Checkpoint | Work | Decision it enables |
| --- | --- | --- |
| 0. Confirm the gap | Read the closest papers; search Google Scholar for 2025-2026 work | Go or adjust the framing (done 2026-09-29) |
| 1. Schema and guide | Draft the schema and one-page labeling guide | Start collecting |
| 2. First 50 | 20 portal, 20 prose, 10 PDF or flyer, labeled; one model run; look at the errors | Is the schema right? Keep going, pause, or bring it to a mentor |
| 3. Full set | Reach about 200 with the format quotas; agreement subset | Run all baselines |
| 4. Results | All models scored, error taxonomy, eligibility-flip check | Write up |
| 5. Write-up | Short paper or report in this structure; public repo | Share with Dr. Alzahrani, decide on Stage 2 |

Preparedness: prior experience with RAG and evaluation sets (PS5 Game Discovery RAG, the capstone's 20-item eval harness), classifier comparison (TakeMeter), Ollama, Docker, Playwright, and Python testing. Data labeling at this scale is new and is the main time cost.

## 10. Expected outcomes and deliverables

1. A public dataset of about 200 labeled CSU opportunity listings with provenance.
2. A labeling guide and schema others can reuse or extend to other university systems.
3. Baseline per-field scores for open-weight models, by format, with an error taxonomy.
4. A count of how often extraction errors would change a student's eligibility decision.
5. A short write-up and a GitHub repo that a stranger can re-run.

What this makes possible: a measured basis for the OpportunityScout idea, since any future matching tool or agent can be graded against this set.

## 11. Resources needed

- **Compute:** a personal laptop with 16 GB RAM runs 3B to 8B models through Ollama. No GPU cluster or paid API is required. Google Colab is a fallback for larger models.
- **Software:** Python, pandas, pytest, Ollama, Docker, Git and GitHub, VS Code. All free.
- **Data:** public CSU pages and PDFs listed in section 6. No student data of any kind.
- **People:** one labeler (the author); a second labeler for the agreement subset is preferred. A faculty mentor is not required for Stage 1 but is the natural reviewer at checkpoint 5.
- **Cost:** none beyond time.

**Platforms and resources**

| Platform | Use in this project | When |
| --- | --- | --- |
| GitHub | Public repo for the dataset, labeling guide, scorer, and write-up; issues as the task list; small commits with clear messages | Stage 1 onward |
| VS Code | Day-to-day editor for the labeling files, scorer, and notebooks; Python and GitHub extensions | All stages |
| Ollama (local) | Runs the open-weight models for the baselines on a personal laptop | Stage 1 |
| Google Colab | Fallback for models too large for the laptop; free GPU for one-off runs | Stage 1, if needed |
| CSUSB HPC | Only if the model set grows beyond what Colab handles; requires lab access, so treat as optional | Stage 1, optional |
| CSU AI Commons | Campus-provided AI tools and training materials; check what is available to students | Stage 1 |
| ChatGPT Edu (CSUSB) | Writing and coding support available to CSUSB students; any AI assistance is disclosed in the write-up | All stages |
| AI/HPC GitHub workshops (CSUSB) | Version control and collaboration practices, if the lab requires them for a later formal submission | Checkpoint 5 |
| Google Scholar, arXiv, ACL Anthology | Literature checks at checkpoint 0 and again before the write-up | Checkpoints 0 and 5 |
| Render or a small VPS, Postgres, FastAPI or Express | Hosting the app and its refresh job | Stage 2 |
| LangGraph, Playwright | Agent loop and page automation, graded against the Stage 1 set | Stage 3 |

Disclosure: this proposal and later code may use AI assistance for drafting and review. The dataset labels are produced by hand, and every result is reproducible from the repo.

## 12. Later stages

**Stage 2, the app.** A site where a CSU student picks a campus and filters by major, GPA, and class year to see everything they are eligible for, including system-wide and research programs. It uses the Stage 1 extractor and a scheduled job that re-checks each source (URL, content hash, last-verified date) and marks stale listings. Most campus scholarships are campus-locked, so the value is in normalized eligibility filtering and the cross-campus programs. Starts with the public sources from section 6 and three or four campuses.

**Stage 3, the agent.** A tool-using agent that finds sources, opens pages, extracts fields, and adds listings on its own, graded against the Stage 1 benchmark. This is the original OpportunityScout idea, now with a way to measure it. Agents are less predictable than fixed pipelines, so the benchmark is what makes the claim testable.

Each stage stands on its own if work stops there.

## Relationship to prior work in the CSUSB AI Research Lab

This grew out of an idea (OpportunityScout, a recommender agent) proposed in Dr. Alzahrani's lab in Spring 2025. The benchmark is new, independent work: a different research question (measurement, not recommendation), different sources, different method, and no reuse of the earlier proposal's text.

## Sources

Papers and datasets

- ScholarSpot: Scholarship Recommendation System using Machine Learning (IEEE ICCUBEA 2024). https://ieeexplore.ieee.org/document/10775136/
- Padiya et al., Enhancing Scholarship Opportunities: A Multi-label Classification Approach (AITA 2024, Springer 2025). https://link.springer.com/chapter/10.1007/978-981-96-1687-9_17 (read in full 2026-09-29)
- Figueroa-Gómez and Galpin, From Text to Decision: A GenAI Framework for Strategic Evaluation of Funding Opportunities (SN Computer Science, 2025). https://link.springer.com/article/10.1007/s42979-025-04304-7 (read in full 2026-09-29)
- Skill-LLM / SkillSpan (arXiv 2410.12052). https://arxiv.org/abs/2410.12052
- SkillSpan (NAACL 2022). https://aclanthology.org/2022.naacl-main.366.pdf
- Chinese-SkillSpan (arXiv 2604.23009). https://arxiv.org/abs/2604.23009
- Enhancing Talent Employment Insights Through Feature Extraction with LLM Finetuning (arXiv 2501.07663). https://arxiv.org/abs/2501.07663
- ExtractBench (arXiv 2602.12247). https://arxiv.org/abs/2602.12247
- LLMStructBench (arXiv 2602.14743). https://arxiv.org/abs/2602.14743
- How Good Are LLMs for Course Recommendation in MOOCs? (arXiv 2504.08208). https://arxiv.org/pdf/2504.08208
- De-conflating Preference and Qualification for Job Recommendation (arXiv 2602.03097). https://arxiv.org/pdf/2602.03097
- Scholarship Finder AI Agent (Kaggle capstone writeup; demo, no data or evaluation). https://www.kaggle.com/competitions/agents-intensive-capstone-project/writeups/scholarship-finder-ai-agent

Standards and checklists

- Datasheets for Datasets (Gebru et al.). https://arxiv.org/abs/1803.09010
- Hugging Face dataset card template. https://huggingface.co/docs/hub/datasets-cards
- NeurIPS 2025 Datasets and Benchmarks call. https://neurips.cc/Conferences/2025/CallForDatasetsBenchmarks
- ACL Rolling Review Responsible NLP checklist. https://aclrollingreview.org/responsibleNLPresearch/
- Who Annotates in NLP? (arXiv 2606.02255). https://arxiv.org/html/2606.02255
- Counting on Consensus (arXiv 2603.06865). https://arxiv.org/html/2603.06865

Data sources checked

- SDSU scholarship portal. https://sdsu.academicworks.com/opportunities
- CSU Fullerton scholarship search. https://fullerton.scholarships.ngwebsolutions.com/Scholarships/Search
- CSULB BeachScholarships. https://csulb.academicworks.com/opportunities
- CSUSB CNS scholarships. https://www.csusb.edu/cns/student-resources/cns-scholarships
- CSUSB CSBS scholarships. https://www.csusb.edu/csbs/our-students/scholarships
- CSUSB CSE ExCELS grant program. https://www.csusb.edu/cse/resources/excels-grant-program
- NSF REU search (crawling disallowed; open individual sites). https://www.nsf.gov/funding/initiatives/reu/search
- Hispanic Scholarship Fund. https://www.hsf.net/scholarship
