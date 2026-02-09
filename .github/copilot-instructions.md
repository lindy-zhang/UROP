## Quick orientation

This repository is a small data-analysis workspace that loads a precomputed evaluation pickle and runs light inspection. Key files:

- `cve_cwe_dataanalysis` — main runnable script (no .py suffix). It prints the current working directory and loads a hard-coded pickle path.
- `eval_results_dataset1_candidates_top5000_cwe-cve_multiprompt.pkl` — primary dataset (pickle) used by the script. Large binary; integrity matters.
- `.venv/` — local Python virtual environment used by the developer.

Keep guidance concise and actionable for an AI coding agent working here.

## Big-picture architecture / intent

- Purpose: load and inspect previously-generated evaluation results (a pickled object), then derive summaries or analysis. There are no services or microservices — it's a single-script analysis pipeline.
- Data flow: script -> loads pickle from repository root -> performs analysis (not present in current file; add downstream analysis functions here).
- Structural choices: the script uses an absolute path to the pickle and monkey-patches `sys.modules['numpy._core'] = np` to support unpickling objects created with older numpy internals.

## Developer workflows (how to run, debug, and modify)

- Run the analysis script (from the repo root):

  ```zsh
  python3 cve_cwe_dataanalysis
  # or explicit interpreter used by developer
  /usr/local/bin/python3 cve_cwe_dataanalysis
  ```

- If you get `_pickle.UnpicklingError: pickle data was truncated`, this almost always indicates the pickle file is corrupt or was incompletely transferred. Actions to take:
  - Verify file size: `ls -lh eval_results_dataset1_candidates_top5000_cwe-cve_multiprompt.pkl` and confirm it matches the expected source copy.
  - Re-download or regenerate the pickle if possible. Do not attempt to silently patch truncated pickles.

- For iterative development, prefer editing the script to use a relative path or env var. Example replacement pattern inside `cve_cwe_dataanalysis`:

  ```py
  path = os.environ.get('EVAL_PKL', os.path.join(os.getcwd(), 'eval_results_dataset1_candidates_top5000_cwe-cve_multiprompt.pkl'))
  ```

## Project-specific conventions and gotchas

- File without `.py`: the main script deliberately has no `.py` suffix. Use `python3 cve_cwe_dataanalysis` (not `python3 cve_cwe_dataanalysis.py`).
- Absolute paths present: several scripts use absolute paths; when making edits prefer relative paths for portability.
- numpy._core monkeypatch: the script sets `sys.modules['numpy._core'] = np` before unpickling. Keep this if unpickling older numpy objects; remove only if you regenerate pickles with current numpy.

## What to modify when adding features

- Add small, testable functions rather than expanding top-level script code. Place helpers in a new module (e.g., `analysis/utils.py`) and import them from the main script.
- When adding reading/writing of large data artifacts, document expected file names and shapes in a `DATA.md` or the top-level `README.md`.

## Integration points & external deps

- Python 3 interpreter and numpy/pandas are required (installed in `.venv`). Prefer using the existing `.venv` for reproducible runs.
- Primary external dependency: the pickled dataset file in repo root. No network services or APIs are referenced.

## Examples for common AI tasks in this repo

- Fix truncated-pickle error: the correct action is to verify and restore the pickle, not to silently wrap `pickle.load` in try/except that hides data loss.
- Make the script robust for development: wrap the path resolution and provide a CLI flag or env var to point to alternative test files.

## Files to reference when making changes

- `cve_cwe_dataanalysis` — follow the existing pattern (cwd print, numpy monkeypatch, absolute path).
- `eval_results_dataset1_candidates_top5000_cwe-cve_multiprompt.pkl` — validate integrity before unpickling.

---
If any parts of the data flow or expected input shapes are unclear, tell me which files or outcomes you need (examples of object contents, sample shapes, or an expected JSON schema) and I will add that to this instruction file.
