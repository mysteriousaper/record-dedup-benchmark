# Duplicate record matching, tested on data I didn't create

SQL (DuckDB) duplicate matching with two tiers: **auto-merge** when the evidence is strong, and **manual review** when it isn't. It is tested on FEBRL, a public record-linkage benchmark from the Australian National University. Its records contain typos, missing values and swapped fields that the benchmark authors added, and it ships with known duplicate pairs.

**How it matches:** only records that share a postcode, surname or ID are compared. Each agreeing field adds points: ID 3, date of birth 2, then first name, surname, address and postcode 1 each. Names and addresses count as agreeing at a Jaro-Winkler similarity of 0.85 or more. A pair with 4+ points is auto-merged. A pair with exactly 3 goes to review.

**How I avoided fooling myself:** I picked the threshold on FEBRL2, then ran it unchanged on FEBRL3, a separate file the rule had never seen.

| Data | Known pairs | Auto-merged | Precision | Recall | Review queue |
|---|---|---|---|---|---|
| FEBRL2 (tuning) | 1,934 | 1,883 | 100% | 97.4% | 19 pairs, 15 true |
| FEBRL3 (unseen) | 6,538 | 6,351 | 100% | 97.1% | 59 pairs, 56 true |

Run it: `pip install duckdb`, then `python benchmark_febrl.py` and `python holdout_febrl3.py`.
