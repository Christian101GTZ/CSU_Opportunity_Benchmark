# Labeling guide

Version 0.4 (09/30/2026). Read `docs/schema.md` first. Each rule decides exactly one thing. When a listing is not covered by a rule, log it in `docs/hard-cases.md` and label it `"not stated"` until a rule is added.

## General rules

1. **Label only what the text states.** If you have to infer it, it is `"not stated"`. "Open to all students" is stated; "the department probably means CS majors" is inference.
2. **One record per award.** A page that lists several awards becomes several records, each with its own `id` and the same `source_url`. A page describing one program with several tiers is one record; put the tiers in `award_amount` as written.
   2b. **Shared page text applies to every award.** Text on the listing page that applies to all its awards (shared requirements, deadlines, default GPA) goes into every record from that page. Text for a single award overrides the shared text. Menus, sidebars, linked pages, and general office text are not shared text.
3. **Quote, do not paraphrase, in `award_amount`.** Shorten only by trimming, never by rewording a figure. (`other_requirements` follows Rule 19.)
4. **Dates use US format** (`MM/DD/YYYY`). A date with no year takes the year that makes it next in the future from `collected_on`, and the case is logged in `hard-cases.md`.
5. **Case and punctuation do not matter for list fields** (`majors`, `class_year`); the scorer normalizes them. Spell them as the page does.

## Field rules

### `opportunity_type`
6. `scholarship` = money for study with no work or research duty. `fellowship` = money tied to a program of activities (research, training, travel). `research program` = a structured research experience (REU, U-RISE, MARC), paid or not. `internship` = work placement. `other` = anything else (travel award, conference grant, competition prize).

### `majors`
7. List the majors the page names. If it names a school or department instead of majors ("School of Computer Science and Engineering"), label the department name as given and log it. If it says any major or all students, label `["any"]`.
8. "STEM" and similar umbrella words are labeled as the word itself (`["STEM"]`), not expanded into majors.

### `min_gpa`
9. Only a number counts. "Good academic standing" is `"not stated"`. If two GPAs appear (cumulative and major), take the cumulative and put the other in `other_requirements`.

### `class_year`
10. Map wording to the five values: "upper division" = `["junior", "senior"]`; "lower division" = `["freshman", "sophomore"]`; "undergraduate" with no further restriction = `["any"]`; "rising junior" = `["sophomore"]` (the student's year when applying). "Graduating senior" = `["senior"]` with a note in `other_requirements`.
11. Unit-count thresholds (for example "60+ units") are not converted to a class year. Put them in `other_requirements` and label `class_year` as `"not stated"`.

### `residency`
12. Any phrasing that requires US citizenship, nationality, or permanent residency = `US citizen or permanent resident`. If DACA recipients are named as eligible but undocumented students in general are not = `DACA eligible`. If undocumented students are explicitly included (with or without DACA), or the page names AB 540 students = `undocumented eligible`. California residency alone = `California resident`. "Open to international students" or no restriction stated = `any` only if openness is stated; otherwise `"not stated"`.

### `financial_need`
13. `required` only if the page says need is required or names a need indicator as a requirement (Pell Grant, FAFSA-demonstrated need). "Preference given to students with need" is `"not stated"` with a note in `other_requirements`.

### `deadline`
14. A deadline is a date by which the application must be submitted. "Reviews begin", "priority date", and "applications open" are not deadlines; put them in `other_requirements` and label `"not stated"` unless a true deadline also appears.
15. A listing that says applications are accepted year-round or on a rolling basis = `rolling`.
16. Two different deadlines on the same page for the same award = `conflict`, with both dates quoted in `other_requirements`. A past deadline is still labeled as written; the dataset is a snapshot.

### `award_amount`
17. Copy the amount phrase as written, trimmed. Keep ranges, "up to", per-year, and duration words. "Varies" is labeled `Varies`, not `"not stated"`.

### `scope`
18. Decided by who may apply, never by which site hosts the listing. A listing on an SDSU page for SDSU students = `campus-only`. A CSU-wide program (Sally Casanova) on an SDSU page = `system-wide`. An NSF REU or a national scholarship = `open`.

### `other_requirements`
19. Short phrases, one requirement each (enrollment status, unit counts, essays, letters, review dates, preferences), using the page's own key words. Copy numbers, dates, names, and qualifiers ("may", "preferred", "at CSUSB") exactly; never add meaning the page does not state. This field is not scored in v0.1; it is for the error analysis and for future schema changes.

## Worked example

**Source:** CSUSB CSE, ExCELS Grant Program, collected 09/30/2026 (`data/raw/csusb-cse-001.txt`).

Page says, in short: NSF-funded scholarships for undergraduates in the School of Computer Science and Engineering; full-time enrollment (12+ units); declared major in Computer Science, Computer Engineering, Computer Systems, Bioinformatics, or Data Science; cumulative GPA 2.7 or higher; evidence of financial need such as Pell Grant; US citizen, national, refugee, or permanent resident; up to $10,000 per year for up to 4 years, 30 awards per year; reviews begin November 1, 2025.

| Field | Label | Rule applied |
| --- | --- | --- |
| `opportunity_type` | `scholarship` | Rule 6: money for study, no work duty |
| `majors` | the five named majors | Rule 7 |
| `min_gpa` | `2.7` | Rule 9 |
| `class_year` | `"not stated"` | No year named; "undergraduate" alone would be `["any"]`, but the page restricts by major, not year, so Rule 10 does not apply; logged as a hard case for Rule 10 wording |
| `residency` | `US citizen or permanent resident` | Rule 12: "citizen, national, refugee, or permanent resident" |
| `financial_need` | `required` | Rule 13: Pell Grant named as evidence |
| `deadline` | `"not stated"` | Rule 14: "reviews begin" is not a deadline |
| `award_amount` | `Up to $10,000 per year for up to 4 years` | Rule 17 |
| `scope` | `campus-only` | Rule 18: CSUSB students only |
| `other_requirements` | `["full-time enrollment (12+ units)", "review begins 11/01/2025", "30 awards per year"]` | Rule 19 |

The full record is in `docs/schema.md`.

## Changes

- 0.1 (09/30/2026): first version with 19 rules and one worked example.
- 0.2 (09/30/2026): added Rule 2b (shared page text applies to every award) after the CSUSB Music and World Languages pages both had page-wide requirements.
- 0.3 (09/30/2026): Rule 3 (exact quoting) now applies to `award_amount` only; Rule 19 sets short phrases in the page's key words for `other_requirements`, with numbers, dates, names, and qualifiers copied exactly.
- 0.4 (09/30/2026): Rule 12 splits `DACA eligible` (DACA only) from `undocumented eligible` (undocumented students, or AB 540 named).
