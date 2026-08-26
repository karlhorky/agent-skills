---
name: code-comments
description: Use when writing or editing code comments in any language
---

# Code Comments

## Overview

Write comments for what the code does not express, usually a reason or behavior. Most comments take one of two forms:

1. Action: start with an imperative verb
2. Fact: start with the subject whose behavior the comment explains

Write actions as `<verb> <noun> <reason>`. Connect an explicit reason with a common connector such as `because`, `so`, `so that`, `to` or `for`.

Exceptions:

- Omit `<reason>` only when the action and nearby code make it clear

Use one sentence with no trailing period, wrapping it across lines when needed. Use multiple paragraphs only when the comment requires background.

**Announce at start:** "I'm using the code comments skill."

## Examples of Code Comments

### Start actions with an imperative verb

#### Example 1: omit a reason clear from nearby code

Good:

```sql
-- Sort optional lectures after other records with same start time
```

#### Example 2: connect an explicit reason with `because`

Good:

```sql
-- Cast to jsonb because SafeQL cannot figure out the type of subqueries
```

#### Example 3: state a selection condition

Good:

```sql
-- Exclude Curriculum Versions already referenced by a cohort for the
-- courses.course_format_id of the outer record
```

#### Example 4: wrap a long reason

Good:

```ts
// Reuse auth from selectCandidateForFusionCandidate without
// duplicating the code and making it harder to maintain
```

#### Example 5: start with filler

Bad:

```ts
// Here we cast to jsonb
```

Why bad:

- Starts with filler instead of the action verb
- Repeats what the code does without explaining why

### Start facts with the subject

Use this form when the comment describes how an external system, library or record behaves rather than what the code does.

#### Example 1: state a SafeQL limitation

Good:

```ts
// SafeQL cannot infer string or number literals
// - https://github.com/ts-safeql/safeql/issues/120
// - https://github.com/ts-safeql/safeql/issues/269
```

#### Example 2: state PostgreSQL aggregate behavior

Good:

```sql
-- PostgreSQL returns NULL instead of an empty array
-- for jsonb_agg when no rows are found
-- - https://www.postgresql.org/docs/current/functions-aggregate.html#:~:text=It%20should%20be,null%20when%20necessary.
```

#### Example 3: state a record rule and its reason

Good:

```sql
-- Flex cohorts have cohorts.campus_id = NULL because
-- no campus services are provided with Flex
```

#### Example 4: state browser behavior and its consequence

Good:

```ts
// Browsers report the canonical 'UTC', never 'Etc/UTC', so this is only
// ever stored when a signup or login sent a time zone missing from this
// list, which makes those records auditable
```

### Name failures with `Avoid` or `Prevent`

Start workaround, guard and fallback comments with `Avoid` or `Prevent`. Name the failure before the mechanism.

#### Example 1: name a user-visible failure

Good:

```sql
-- Avoid failing signup when the browser reports a time zone missing
-- from time_zones, falling back to 'Etc/UTC' to keep those records
-- auditable
```

#### Example 2: name a test failure

Good:

```ts
// Avoid test failures with Winter 2022 test cohort in
// `schedulerReschedulesCohortsCurriculaAppointments.spec.ts`
// because the `selectCohortForLearnCohort` `INNER JOIN` will
// cause "Not Found" error for any cohort without a Curriculum
// Version
```

#### Example 3: name a prevented failure

Good:

```ts
// Prevent parallel runs from deleting each other's records
```

#### Example 4: start with the mechanism

Bad:

```sql
-- Fall back to 'Etc/UTC'
```

Why bad:

- Starts with the fallback mechanism instead of the signup failure
- Omits the missing time zone and auditability reasons

### Name exact identifiers

Name exact tables, columns, functions and types so comments remain searchable. Avoid vague nouns such as "the date field":

#### Example 1: name a table column

Good:

```sql
-- Include enrolled_cohorts.end_date for ORDER BY clause
```

#### Example 2: name an ordering column

Good:

```sql
-- Ordering by events.id required for SELECT DISTINCT ON
```

#### Example 3: use a vague noun

Bad:

```sql
-- Include the end date so the sorting works
```

### Use comment prefixes

Use `TODO:` for a required future action, `Security:` for a security constraint, `Note:` for background and `Source:` for a link or quote.

#### Example 1: state a required action with `TODO:`

Good:

```ts
// TODO: Convert sort_order to a self-referencing foreign key
// to create a data structure similar to a linked list
```

#### Example 2: state a security reason with `Security:`

Good:

```sql
-- Security: Avoid exposing the private URL
-- unnecessarily by returning a boolean
```

#### Example 3: cite a link with `Source:`

Good:

```sql
-- Source: https://developers.google.com/meet/api/guides/overview#meeting-code
```

### Use long comments for background

Use a long comment only when complex or intermittent behavior requires history to prevent reopening a resolved decision. Separate paragraphs with a bare comment marker, prefix quotes with `>` and end with a `Source:` link.

#### Example 1: quote external documentation

Good:

```sql
-- Note: The Google Meet REST API documentation recommends against
-- storing the meeting code, because it expires 365 days after last
-- use, given there are no future calendar events associated with it:
--
-- > Meeting codes shouldn't be stored long term as they can become
-- > dissociated from a meeting space and can be reused for different
-- > meeting spaces in the future. Generally, meeting codes expire
-- > 365 days after last use. For more information, see Learn about
-- > meeting codes in Google Meet (https://support.google.com/meet/answer/10710509)
--
-- Source: https://developers.google.com/meet/api/guides/overview#meeting-code
```
