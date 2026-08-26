---
name: code-comments
description: Use when writing or editing a code comment, in any language
---

# Code Comments

## Overview

A comment earns its place by carrying what the code cannot: the reason. Almost all comments take one of two shapes, chosen by what the comment is about:

1. Action: open with an imperative verb naming what the code does
2. Fact: open with the subject when stating how an external system or the data behaves

Both connect to their reason with `because`, `so`, `so that`, `to` or `for`. Keep to one sentence with no trailing period, unless the situation genuinely needs background.

**Announce at start:** "I'm using the code comments skill."

## Examples of Code Comments

### Action: open with an imperative verb

Common verbs: `Avoid`, `Prevent`, `Deny`, `Allow`, `Cast`, `Sort`, `Filter out`, `Skip`, `Exclude`, `Restrict`, `Keep`, `Move`, `Hide`, `Remove`, `Reuse`, `Include`, `Return`, `Raise`, `Enforce`

Good:

```sql
-- Sort optional lectures after other records with same start time
-- Cast to jsonb because SafeQL cannot figure out the type of subqueries
-- Exclude Curriculum Versions already referenced by a cohort
```

```ts
// Filter out undefined curriculumModuleCategories for older curricula
// Reuse auth from selectCandidateForFusionCandidate without duplicating checks
```

Bad:

```ts
// This function is used to sort the lectures
// Here we cast to jsonb
```

Why bad: opens with filler (`This function`, `Here we`) instead of the verb, and states what the code already shows without the reason.

### Fact: open with the subject

Use when the comment describes how an external system, a library or the data behaves, rather than what this code does.

Good:

```ts
// SafeQL cannot infer string or number literals
// PostgreSQL returns NULL instead of an empty array
// Browsers report the canonical 'UTC', never 'Etc/UTC'
```

```sql
-- Flex cohorts have cohorts.campus_id = NULL because no campus services are provided with Flex
-- Meeting codes expire 365 days after last use
```

The subject is the thing that behaves surprisingly. Naming it first tells the reader immediately whose rule they are up against.

### Failures: use `Avoid` or `Prevent`, and name the failure

When a comment explains a workaround, a guard or a fallback, the first words must be the failure that would otherwise happen, not the mechanism that avoids it.

Good:

```sql
-- Avoid failing signup when the browser reports a time zone missing from time_zones
```

```ts
// Prevent long titles in multi-day events from overflowing the row
// Avoid test failures with Winter 2022 test cohort in a past trimester
```

Bad:

```sql
-- Fall back to the home campus zone rather than failing signup, since the time zone list was copied from Intl.supportedValuesOf('timeZone') in Nov 2022
```

Why bad: opens with the mechanism (`Fall back to`), buries the user-visible failure in the middle, and spends the rest of the line on provenance. The reader has to reach the end to learn what breaks.

### Name exact identifiers

Use the real table, column, function and type names. A comment that says "the date field" ages badly and cannot be searched for.

Good:

```sql
-- Include enrolled_cohorts.end_date for ORDER BY clause
-- Ordering by events.id required for SELECT DISTINCT ON
```

Bad:

```sql
-- Include the end date so the sorting works
```

### Prefix a comment when it belongs to a known class

`TODO:`, `FIXME:`, `Security:`, `Note:`, `Source:`, `Caveats:`

Good:

```ts
// TODO: Convert sort_order to a self-referencing foreign key
// Security: Avoid exposing the private URL
// Source: https://developers.google.com/meet/api/guides/overview#meeting-code
```

A `TODO:` states the action to take, not the problem in the abstract.

### Exception: long background comments

Complex or intermittent situations need history to understand, and a single line cannot carry it. Use multiple paragraphs separated by a bare comment marker, quote external documentation with `>`, and end with a `Source:` link.

```sql
-- Note: The Google Meet REST API documentation recommends against
-- storing the meeting code, because it expires 365 days after last
-- use, given there are no future calendar events associated with it:
--
-- > Meeting codes shouldn't be stored long term as they can become
-- > dissociated from a meeting space and can be reused for different
-- > meeting spaces in the future. Generally, meeting codes expire
-- > 365 days after last use.
--
-- Source: https://developers.google.com/meet/api/guides/overview#meeting-code
```

Reserve this for cases where a reader would otherwise reopen a decision that was already settled. Everything else stays one line.
