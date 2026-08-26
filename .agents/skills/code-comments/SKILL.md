---
name: code-comments
description: Use when writing or editing code comments in any language
---

# Code Comments

## Overview

Write comments for what the code does not express, usually a reason or behavior. Most comments take one of three forms:

1. Action: start with an imperative verb
2. Fact: omit the action and state a reason or behavior with its subject first
3. Description: use a noun phrase for what a database field or TypeScript property stores

Write actions as `<verb> <noun> <reason>`. Common words before an explicit reason include `because`, `so`, `so that`, `to` and `for`.

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

#### Example 3: connect an explicit reason with `for`

Good:

```sql
-- Exclude Curriculum Versions already referenced by a cohort for the
-- courses.course_format_id of the outer record
```

#### Example 4: connect an explicit reason with `to`

Good:

```ts
// Reuse auth from selectCandidateForFusionCandidate to avoid
// duplicating the code and making it harder to maintain
```

#### Example 5 (bad): start with filler

Bad:

```ts
// Here we cast to jsonb
```

Why bad:

- Starts with filler instead of the action verb
- Repeats what the code does without explaining why

### Start facts with the subject

Use this form when nearby code expresses the action and the comment only needs to state its reason or behavior.

#### Example 1: state a fact without the action

Good:

```ts
// SafeQL cannot infer string or number literals
// - https://github.com/ts-safeql/safeql/issues/120
// - https://github.com/ts-safeql/safeql/issues/269
```

#### Example 2: state a fact without the action

Good:

```sql
-- PostgreSQL returns NULL instead of an empty array
-- for jsonb_agg when no rows are found
-- - https://www.postgresql.org/docs/current/functions-aggregate.html#:~:text=It%20should%20be,null%20when%20necessary.
```

#### Example 3: state a fact without the action

Good:

```sql
-- Flex cohorts have cohorts.campus_id = NULL because
-- no campus services are provided with Flex
```

#### Example 4: state a fact without the action

Good:

```ts
// Browsers report the canonical 'UTC', never 'Etc/UTC', so this is only
// ever stored when a signup or login sent a time zone missing from this
// list, which makes those records auditable
```

### Describe database fields and TypeScript properties with a noun phrase

Write database field and TypeScript property descriptions directly above their declarations, omitting the identifier and verb. Use TSDoc `/** */` on TypeScript properties for editor integration such as documentation on hover, and repeat the text as a `--` comment above the matching field in `CREATE TABLE`. Add a reason only for a non-obvious purpose.

#### Example 1: describe a TypeScript property

Good:

```ts
/** Country subdivision (province or state) */
subdivision: string | null;
```

#### Example 2: describe a matching database field

Good:

```sql
-- Country subdivision (province or state)
subdivision varchar(80),
```

#### Example 3: describe a TypeScript property and add a non-obvious reason

Good:

```ts
/**
 * Assessor's time zone at the moment of assessment, so the
 * certificate prints the same date for every viewer
 */
timeZoneId: TimeZone['id'];
```

### Put debugging details first

Comments are often read while debugging, so two things are critical for speed of scanning:

1. The first word of the comment
2. The words near the start of the comment

Start actions with a verb followed by affected records or entities. Start facts with affected records or entities. Put generic conditions and reasons afterward.

#### Example 1: action verb directly followed by affected records

Good:

```sql
-- Exclude Tech Fundamentals Foundations (Immersive) cohorts, which
-- never had graduation events, so their certificates have no end
-- date to print
```

#### Example 2 (bad): put a generic condition first

Bad:

```sql
-- Return no certificate for Immersive cohorts with no graduation event,
-- because there is no end date to print - currently only Tech
-- Fundamentals Foundations (Immersive) cohorts
```

Why bad:

- Names the affected cohorts on the last line
- Starts with a generic condition that does not identify the affected cohorts

### Name failures with `Avoid` or `Prevent`

Start workaround, guard and fallback comments with `Avoid` or `Prevent`. Follow the action form above, naming the failure before the reason (`<Avoid/Prevent> <failure> <reason>`). Common words before the reason include `by` and `with`.

#### Example 1: name the failure before the reason

Good:

```ts
// Prevent parallel runs from deleting each other's records
```

#### Example 2: name the failure before the reason

Good:

```ts
// Avoid test failures with Winter 2022 test cohort in
// `schedulerReschedulesCohortsCurriculaAppointments.spec.ts`
// because the `selectCohortForLearnCohort` `INNER JOIN` will
// cause "Not Found" error for any cohort without a Curriculum
// Version
```

#### Example 3: name the failure before the reason

Good:

```sql
-- Avoid signup failure when the browser reports a time zone missing
-- from time_zones by falling back to 'Etc/UTC'
```

#### Example 4 (bad): start with the fallback

Bad:

```sql
-- Fall back to 'Etc/UTC'
```

Why bad:

- Starts with the fallback instead of the failure
- Omits the missing time zone reason

### Name exact identifiers

Name exact tables, database fields, functions and types so comments remain searchable. Avoid vague nouns such as "the date field":

If multiple identifiers could be used, use the one closest to the comment. For example, in a React component, name the field it reads (`campusCity`), while in a SQL query or table definition, name the database field it uses (`cohorts.campus_id`) so readers can search the current file.

#### Example 1: name a table field

Good:

```sql
-- Include enrolled_cohorts.end_date for ORDER BY clause
```

#### Example 2: name an ordering field

Good:

```sql
-- Ordering by events.id required for SELECT DISTINCT ON
```

#### Example 3 (bad): use a vague noun

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
