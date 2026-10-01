# CSU Opportunity Extraction Benchmark

A public, human-labeled dataset of undergraduate opportunity listings (scholarships, fellowships, research programs) from California State University, San Bernardino (CSUSB), and baseline results for how well open-weight language models extract their eligibility fields.

**Status:** Stage 1, Step 3: labeling guide review. Guide v0.4.2 tested on 13 non-STEM awards (CSUSB Music, World Languages); scope set to CSUSB only (about 180 listings); next: a second person labels one listing using only the guide.

**Research question:** How reliably can open-weight LLMs extract eligibility fields (major, GPA, class year, residency, deadline, award amount, scope) from real CSUSB opportunity listings, and which fields and formats fail?

## Why

Opportunity listings are published as portal pages, department prose, PDFs, flyers, and emails. Any tool that matches students to opportunities has to extract eligibility from that text first, and there is no public dataset to measure how well models do it. This benchmark fills that gap. Full rationale, related work, and method: `PROPOSAL.md`.

## Repository layout

```
.
├── README.md              this file: overview, status, how to run
├── PROPOSAL.md            research proposal: problem, related work, method, timeline
├── TODO.md                step-by-step work plan with "done when" criteria
├── CITATION.cff           how to cite this dataset
├── LICENSE                MIT (code)
├── requirements.txt
├── docs/
│   ├── DECISIONS.md       decision log: what changed, when, and why
│   ├── schema.md          the fields, types, and the scoring metric per field
│   ├── labeling-guide.md  labeling rules, one decision per rule, with a worked example
│   ├── hard-cases.md      running list of ambiguous listings and how they were resolved
│   ├── sources.md         every source used, its access status, robots.txt check, date
│   └── DATASET_CARD.md    dataset card (Hugging Face template), filled at release
├── data/
│   ├── LICENSE            CC BY 4.0 (data)
│   ├── sources.csv        id, source_url, campus, format, collected_on
│   ├── raw/               raw text or PDF per listing, named by id
│   └── labeled/
│       └── listings.jsonl gold labels, one record per line
└── eval/
    ├── extract.py         runs a model over data/raw and writes JSON outputs
    ├── score.py           scores outputs against gold, per field
    ├── test_score.py      pytest for the scorer
    ├── outputs/           model outputs, one folder per model
    └── results/           score tables and the error taxonomy
```

## How to run (once Step 6 exists)

```bash
git clone https://github.com/Christian101GTZ/CSU_Opportunity_Benchmark
cd CSU_Opportunity_Benchmark
pip install -r requirements.txt
ollama pull qwen2.5:7b
python eval/extract.py --model qwen2.5:7b
python eval/score.py --model qwen2.5:7b
```

## How this project is documented

- **Every decision has a written reason** in `docs/DECISIONS.md`: date, what changed, why, what it affects.
- **Every listing has provenance:** source URL, campus, format, and collection date in `data/sources.csv`. The dataset is a frozen snapshot; it is not maintained as current.
- **Every labeling rule decides one thing** (`docs/labeling-guide.md`), and ambiguous cases are logged rather than silently resolved (`docs/hard-cases.md`).
- **Every number is reproducible:** the scorer is tested, outputs are saved, and results are regenerated from the repo.
- **Limitations are stated,** including single-annotator labeling and the size of the set.
- **Commits are small** with messages that say what and why.

## Ethics and data use

Only public pages and documents are collected; nothing behind a login. robots.txt and site terms are checked per domain and recorded in `docs/sources.md`. No student data of any kind is collected. The dataset is released under CC BY 4.0; code under MIT.

## Relationship to prior work

This grew out of an idea (OpportunityScout, a recommender agent) proposed in the CSUSB AI Research Lab in Spring 2025. The benchmark is new, independent work with a different research question, sources, and method.

## AI-assistance disclosure

Drafting and code review used AI assistance. All labels are produced by hand, and all results are reproducible from this repository.

## Citation

See `CITATION.cff`.
