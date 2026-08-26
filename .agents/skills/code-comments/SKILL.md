---
name: code-comments
description: Use when writing or editing code comments in any language
---

# Code Comments

## Overview

Write comments for what the code does not express, usually a reason or behavior. Most comments take one of two forms:

1. Action: start with an imperative verb
2. Fact: start with the subject whose behavior the comment explains

Write actions as `<verb> <noun> <reason>`. Connect every action to its reason with a common connector such as `because`, `so`, `so that`, `to` or `for`. Use one sentence on one line with no trailing period unless the comment requires background.

**Announce at start:** "I'm using the code comments skill."

## Examples of Code Comments

### Start actions with an imperative verb

Good:

```sql
-- Sort optional lectures after other records with same start time
```

```sql
-- Cast to jsonb because SafeQL cannot figure out the type of subqueries
```

```sql
-- Exclude Curriculum Versions already referenced by a cohort
```

```ts
// Filter out undefined curriculumModuleCategories for older curricula
```

```ts
// Reuse auth from selectCandidateForFusionCandidate without duplicating checks
```

Bad:

```ts
// This function is used to sort the lectures
```

```ts
// Here we cast to jsonb
```

Why bad:

- Starts with filler instead of the action verb
- Repeats what the code does without explaining why

### Start facts with the subject

Use this form when the comment describes how an external system, library or record behaves rather than what the code does.

Good:

```ts
// SafeQL cannot infer string or number literals
```

```ts
// PostgreSQL returns NULL instead of an empty array
```

```ts
// Browsers report the canonical 'UTC', never 'Etc/UTC'
```

```sql
-- Flex cohorts have cohorts.campus_id = NULL because no campus services are provided with Flex
```

```sql
-- Meeting codes expire 365 days after last use
```

### Name failures with `Avoid` or `Prevent`

Start workaround, guard and fallback comments with `Avoid` or `Prevent`. Name the failure before the mechanism.

Good:

```sql
-- Avoid failing signup when the browser reports a time zone missing from time_zones
```

```ts
// Prevent long titles in multi-day events from overflowing the row
```

```ts
// Avoid test failures with Winter 2022 test cohort in a past trimester
```

Bad:

```sql
-- Fall back to the home campus zone rather than failing signup, since the time zone list was copied from Intl.supportedValuesOf('timeZone') in Nov 2022
```

Why bad:

- Starts with the fallback mechanism instead of the signup failure
- Ends with source history instead of the condition that causes the failure

### Name exact identifiers

Name exact tables, columns, functions and types so comments remain searchable. Avoid vague nouns such as "the date field":

Good:

```sql
-- Include enrolled_cohorts.end_date for ORDER BY clause
```

```sql
-- Ordering by events.id required for SELECT DISTINCT ON
```

Bad:

```sql
-- Include the end date so the sorting works
```

### Use comment prefixes

Use `TODO:` for a required future action, `Security:` for a security constraint, `Note:` for background and `Source:` for a link or quote.

Good:

```ts
// TODO: Convert sort_order to a self-referencing foreign key
```

```ts
// Security: Avoid exposing the private URL
```

```ts
// Source: https://developers.google.com/meet/api/guides/overview#meeting-code
```

### Use long comments for background

Use a long comment only when complex or intermittent behavior requires history to prevent reopening a resolved decision. Separate paragraphs with a bare comment marker, prefix quotes with `>` and end with a `Source:` link.

Good:

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
