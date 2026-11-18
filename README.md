# Project1_Next_Gen_Seq_Analysis

## Mismatch analysis of NGS reads

### Purpose
This program compares sequencing reads from six *S. pombe* samples with a 200 nucleotide reference sequence to find mismatches. It asks whether a genotoxic agent changes mutation patterns compared with untreated controls (n=3 per group).

### How to run
Open `C7147_Project_1_assessment.ipynb` in Google Colab and choose Runtime > Run all. The setup cells download `reference.txt` and `reads.csv`, so nothing needs uploading.The full run of all cells in order will provide all of the outputs of the program, cell-by-cell.

### Inputs and outputs
The program takes a reference string and a list of read dictionaries (`read_id`, `sample`, `group`, `sequence`). It returns an annotated list of reads (status, errors, mutations) and a summary dictionary per-sample. It prints a results table and a substitution table for each group, then it compares the three most mismatched positions between treatment and control groups with Welch's t-test, printing one line per position.

### Code design
- **Counting separated from printing.** `summarise_samples` only counts, `print_group_summaries` only prints, and `calculate_percentage` is shared with Step 5. One long function would have repeated the zero-valid-reads check and likely been harder to test.
- **Dictionary of position counts.** I stored `position_counts` as `{position: count}` instead of a list as long as the read. The summary doesn't hold the read length, and a dictionary means positions with no mismatches are never picked when fewer than three exist.
- **Keeping invalid reads.** `analyse_reads` keeps invalid reads with `status="invalid"` and no mutations rather than dropping them, so Step 4 can count them in `invalid_reads`.

### Testing
1. Step 3 mixed `analyse_reads` test (lower-case read repaired, wrong length, invalid characters) checked against a pseudorandom “expected” list.
2. Step 4 with empty input (returns `{}`) and a sample with no valid reads (`mismatch_percentage` is `None`).
3. Step 5 with new pseudorandom data where one sample has no valid reads and another group is ignored. One sample per group or no variation returns `None` statistics instead of crashing.
