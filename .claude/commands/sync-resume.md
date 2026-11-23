---
description: Propagate changes in the resume tex sources to the CN resume, website, and copy sheets, touching only what changed since the last sync.
---

Sync my resume changes since the last sync. Only propagate what changed. Never regenerate a target wholesale.

## Sources, snapshots, targets

| Source (I edit this) | Snapshot of last sync | Targets |
|---|---|---|
| `resume_EN/resume.tex` | `.claude/sync/resume_EN.synced.tex` | `resume_CN/resume-zh_CN.tex`, `index.html`, `../career/resume-copy-en.txt` |
| `resume_CN/resume-zh_CN.tex` | `.claude/sync/resume_CN.synced.tex` | `../career/resume-copy-zh.txt` |

`../career` is my private repo, which sits next to this one. If it is missing, skip the copy-sheet targets and tell me.

## Steps

1. Diff each source against its snapshot:
   `git diff --no-index --word-diff .claude/sync/resume_EN.synced.tex resume_EN/resume.tex` (same for CN).
   If a snapshot does not exist, stop and ask me rather than syncing everything.
   If both diffs are empty, say "nothing to sync" and stop.
2. List the changed entries in plain language (added, removed, reworded bullets; changed dates or titles; added or removed whole entries). Ignore pure layout changes (`\vspace`, spacing, line wrapping) and changes inside `%` comments, unless an entry was commented in or out. That counts as an add or a remove.
3. Apply the EN changes to each target, editing only the matching entries:
   - **CN tex**: rewrite each changed item in natural Chinese resume style. Restructure the sentence instead of translating word for word, and follow the conventions of the surrounding CN text (date format `2026.1 - 2026.5`, `\datedsubsection`, `|` between org and role). The CN resume has entries the EN one doesn't (and vice versa, e.g. the CUHK education and IEEE role), and its wording has been hand-tuned. Leave every entry that didn't change in EN untouched. If an EN entry was removed, ask me before removing it from CN.
   - **index.html**: the website is the longer, more detailed version. For a reworded bullet, update the website bullet so it carries the new fact, but keep any extra detail the website already has. For a new entry, write it slightly more detailed than the resume, matching the surrounding HTML structure. Don't touch sections that have no EN change.
   - **resume-copy-en.txt**: plain text only. Strip all LaTeX (`\textbf{}`, `\href{}{}`, `\%` → `%`, `$r \leq 0.3$` → `r ≤ 0.3`). Keep the `# Label` / value-on-its-own-line layout and `•` bullets. Dates are `YYYY-MM`.
4. Apply CN-source changes, including the ones step 3 just made to the CN tex, to `resume-copy-zh.txt` with the same plain-text rules. Dates are `YYYY.MM`.
5. If either copy sheet changed, rebuild the clickable HTML version: `py ../career/build-copy-html.py` (writes `../career/resume-copy.html`, which is gitignored).
6. Update both snapshots to the current sources:
   `cp resume_EN/resume.tex .claude/sync/resume_EN.synced.tex` and `cp resume_CN/resume-zh_CN.tex .claude/sync/resume_CN.synced.tex`.
7. Report briefly what changed in each target. Remind me to review with `git diff` here and in `../career`, and to rerun `/sync-resume` after I hand-edit the CN tex so the zh copy sheet catches up. Don't commit or push.

LinkedIn and the GitHub profile README are not targets. I update them by hand, using the copy sheets.
