---
name: surgical-commits
description: Use when you have made file changes and think you're ready to commit
---

# Surgical Commits

## Overview

To make commits clear and easy for both humans and machines to review, make surgical commits:

1. Reduce the diff as much as possible, without dropping any functionality or violating any of your other rules
2. If the changes are easier to review in multiple commits, then split cleverly into multiple commits which avoid or reduce diffs or make diffs easier to statically verify, using Git tricks like rename detection
3. Since AI changes are known to be overly additive in nature, work against this intentionally - try to do everything possible to maintain code size or even reduce it

Good:

- Reducing review effort
- Making diffs statically analyzable / verifiable
- Using clever Git tricks (but not too clever)

Bad:

- Code golfing to reduce diffs
- Being too clever while trying to reduce diffs and making commits much harder to follow
- Removing features to reduce diffs
- Reducing tests to reduce diffs
- Violating any of your rules or instructions to reduce diffs
- Undermining quality in any other way to reduce diffs

**Announce at start:** "I'm using the surgical commits skill."

## Examples of Surgical Commits

### Split into commits: isolate moves from semantic changes

When a diff combines renaming, moving code and behavior changes, split it before committing.

Goal:

- Make each commit have one review purpose
- Preserve exact code lines as much as possible
- Put semantic edits after mechanical edits
- Let reviewers verify moved code stayed identical, eg. by replacing the new block with the old block and checking for no diff, using `git diff --no-index --ignore-all-space` on extracted blocks, or using moved-line-aware diff tooling

Good split:

```text
Commit 1: Rename file only
- Rename seedInitial.ts to seed.ts
- Preserve file contents exactly
- Goal: GitHub can show this as a pure rename with no diff

Commit 2: Move code only
- Move selected code into the renamed / new file
- Preserve exact lines and indentation as much as possible, even if not finally formatted
- Goal: reviewers can verify the move without whitespace / indentation noise

Commit 3: Change behavior
- Add fixture seeding behavior
- Update only required call sites
- Goal: semantic changes are isolated from mechanical churn
```

Bad split:

```text
Commit 1:
- Rename seedInitial.ts to seed.ts
- Move seed helper code
- Extract helpers
- Add fixture seeding behavior
- Update unrelated files
```

Why bad:

- Mechanical movement hides semantic changes
- Rename detection may become noisy or fail
- Reviewers must manually re-verify moved code
- The commit is harder for humans and AI tools to review

Temporary invalid states are acceptable if they make mechanical commits easier to verify.
