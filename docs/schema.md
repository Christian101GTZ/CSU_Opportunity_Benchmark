# Schema

Version 0.2 (09/30/2026). One record per award. A field the listing does not state is `"not stated"`, never guessed.
The scoring column says how `eval/score.py` compares a model output to the gold label for that field.

## Provenance fields (filled at collection, not by the model)

| Field | Type | Example | Notes |
| --- | --- | --- | --- |
| `id` | string | `csusb-cse-001` | `<campus>-<source>-<nnn>`; one id per award, so a multi-award page gets several ids with the same `source_url` |
| `source_url` | string | `https://www.csusb.edu/cse/resources/excels-grant-program` | The page or file the text came from |
| `campus` | string | `CSUSB` | The campus that publishes the listing; `CSU` for system-wide; `external` for non-CSU |
| `format` | one of `portal`, `prose`, `pdf`, `flyer`, `email` | `prose` | How the text was published |
| `collected_on` | US date (MM/DD/YYYY) | `09/30/2026` | The dataset is a frozen snapshot |
| `raw_file` | string | `data/raw/csusb-cse-001.txt` | Saved text or PDF |

## Labeled fields (the model extracts these)

| Field | Type | Example | Scoring |
| --- | --- | --- | --- |
| `opportunity_type` | one of `scholarship`, `fellowship`, `research program`, `internship`, `other` | `scholarship` | exact |
| `majors` | list of strings, or `["any"]`, or `"not stated"` | `["Computer Science", "Computer Engineering", "Computer Systems", "Bioinformatics", "Data Science"]` | set F1 after lowercasing and trimming |
| `min_gpa` | number, or `"not stated"` | `2.7` | exact after rounding to 2 decimals |
| `class_year` | list from `freshman`, `sophomore`, `junior`, `senior`, `graduate`, or `["any"]`, or `"not stated"` | `["junior", "senior"]` | set F1 |
| `residency` | one of `US citizen or permanent resident`, `DACA eligible`, `undocumented eligible`, `California resident`, `any`, `not stated` | `US citizen or permanent resident` | exact |
| `financial_need` | one of `required`, `not stated` | `required` | exact |
| `deadline` | US date (MM/DD/YYYY), or `rolling`, or `conflict`, or `"not stated"` | `02/13/2026` | exact after date normalization |
| `award_amount` | string as written, or `"not stated"` | `Up to $10,000 per year for up to 4 years` | token F1 after lowercasing; dollar figures must match exactly |
| `scope` | one of `campus-only`, `system-wide`, `open` | `campus-only` | exact |
| `other_requirements` | list of short strings, or `[]` | `["full-time enrollment (12+ units)"]` | not scored in v0.1; kept for error analysis |

## Record-level metrics

- **Valid JSON rate:** share of model outputs that parse and contain every labeled field.
- **Whole-record accuracy:** share of records where every scored field is correct.
- **Per-format breakdown:** every metric reported separately for `portal`, `prose`, `pdf`, `flyer`, `email`.

## Example record (gold)

```json
{
  "id": "csusb-cse-001",
  "source_url": "https://www.csusb.edu/cse/resources/excels-grant-program",
  "campus": "CSUSB",
  "format": "prose",
  "collected_on": "09/30/2026",
  "raw_file": "data/raw/csusb-cse-001.txt",
  "opportunity_type": "scholarship",
  "majors": ["Computer Science", "Computer Engineering", "Computer Systems", "Bioinformatics", "Data Science"],
  "min_gpa": 2.7,
  "class_year": "not stated",
  "residency": "US citizen or permanent resident",
  "financial_need": "required",
  "deadline": "not stated",
  "award_amount": "Up to $10,000 per year for up to 4 years",
  "scope": "campus-only",
  "other_requirements": ["full-time enrollment (12+ units)", "review begins 11/01/2025"]
}
```

## Changes

- 0.1 (09/30/2026): first version.
- 0.2 (09/30/2026): `residency` split: `DACA eligible` (DACA recipients only) and `undocumented eligible` (undocumented students with or without DACA, or AB 540 students). See `docs/DECISIONS.md`. Added `financial_need` and `other_requirements` after the first worked example showed both are common eligibility statements (see `docs/DECISIONS.md`).
