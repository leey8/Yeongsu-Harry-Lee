---
type: prompt-log
owner: yeongsu-harry-lee
started: 2026-09-07
---

# Prompt log

Record only AI sessions that materially change the repository. Include the date, the engagement, the requested outcome, and the resulting files.

| Date | Tool | What I asked | What I got | What I did with it |
|---|---|---|---|---|
| 2026-09-07 | Claude (Cowork) | Set up the repository foundation for the term | Canonical conventions plus scaffolding for the engagement, brief, decision, data, and analysis directories | Reviewed the structure and committed it as my workspace skeleton |
| 2026-09-07 | Claude (Cowork) | Draft AI interaction guidelines for this repository | Human-ownership, academic-integrity, permitted-use, prohibited-use, and transparency conventions | Checked them against the course house rules and committed them to `AGENTS.md` |
| 2026-09-18 | Claude (Cowork) | Explain the professor's Stage 0 feedback and how to fix each item | Step-by-step GitHub instructions for the `.gitignore` patterns, the two missing READMEs, `test_dir/`, and my Git identity | Made and reviewed every edit myself: updated `.gitignore` (Office lock files, `Thumbs.db`, Copilot worktrees), added `analysis/README.md` and `docs/README.md`, removed `test_dir/`, removed street address and phone from `RESUME.md`, set my Git identity |
| 2026-09-26 | Claude (Cowork) | Attack my committed brief, using the Stage 1 prompt verbatim | Implicit assumptions (objective function never stated, all-64-beds premise, binding constraint unnamed, owner-hour costing undefined), unsupported claims (compared diminishing-return rates without comparing revenue per bed or base labor hours), three client questions, and four gaps in my falsification condition | Left the committed prediction unchanged; recorded the points as Stage 2 test points and Stage 3 judgment criteria |
| 2026-10-14 | Claude (Cowork) | Review my draft spec before committing it | Five rounds of review: missing formula definitions, leftover citation markers from another tool, exact-equality checks that would fail on floating point, and an MC-dip explanation that belonged in Stage 1.3 | Fixed each one myself and committed the spec before opening Excel |
| 2026-10-14 | Claude Code | Build the workbook from spec.md; stop and ask rather than guessing | Stopped before building: net profit came out $42,768.33 against the $42,762 check figure, the standalone tomato count came out 7, plus six unspecified items. After I answered each in the spec it built the workbook, and check 7 in its per-block form failed five deliberately broken copies as intended | Answered every question in `spec.md` and recommitted before each build (modified) |
| 2026-10-15 | Claude Code | Rewrite check 7 so the spec matches the workbook | Per-block named ranges with the exclusions stated | Reviewed the wording and committed it (used) |

## Errors caught

- 2026-10-15 — The case states $34.72/hr and $17.36/hr, and taking those at face value left net profit $6.33 above the published check figure. Cross-checking the Farm Profit Lab (labor $104,118, profit $42,762, +1 tomato bed −$591) showed both are rounded displays of 25000/720 and 25000/1440. Redefined both rates as formulas in spec §2.
