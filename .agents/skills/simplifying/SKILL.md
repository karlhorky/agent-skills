---
name: simplifying
description: Use when you are near the end of a task, to complete a final polishing and cleanup round before letting the user review
---

# Simplify

## Overview

Review code you have created to simplify, by re-reviewing your code change against:

1. The code style guidelines in `agents.md`
2. Code style and conventions in files similar to those touched by your change
3. Examples of simplification below

Assume your changes were made with zero knowledge of the codebase code style and conventions and only approve changes that satisfy the above criteria.

**Announce at start:** "I'm using the simplifying skill."

## Examples of Simplification

### Reduce complexity: Eliminate unnecessary variables

As code evolves, it is easy to accumulate single-use variables added for "readability", but they rarely make code easier to read and increase cognitive load:

- Increased indirection
- Introduction of long-lived and long-accessible identifiers

Exceptions:

- Multiple usages of the same value
- Aliasing for TypeScript type guards, where a named variable can help TypeScript narrow types reliably across multiple checks or in complex expressions
- Aliasing for complex expressions where name adds clarity to the intent (alternative: use a code comment)

#### Example 1: avoid chaining single-use aliases

Before:

```ts
const memes = selectAll('li.meme').map((li) => {
  const img = li.querySelector('img')!;
  const src = img.getAttribute('src')!;
  const title = li.querySelector('.caption')!.textContent.trim();

  return {
    src,
    title: title.split('\n')[0]!,
  };
});
```

After:

```ts
const memes = selectAll('li.meme').map((li) => {
  return {
    src: li.querySelector('img')!.getAttribute('src')!,
    title: li.querySelector('.caption')!.textContent.trim().split('\n')[0]!,
  };
});
```

#### Example 2: avoid single-use aliases like `ok`

Before:

```ts
const results = items.filter((item) => {
  const ok = isValid(item);
  return ok;
});
```

After:

```ts
const results = items.filter((item) => {
  return isValid(item);
});
```

#### Example 3: inline single-use variables

Before:

```ts
const outputDirPaths = files.outputFiles.map(({ path }) => {
  return dirname(path);
});

const uniqueOutputDirPaths = [...new Set(outputDirPaths)];

await pMap(uniqueOutputDirPaths, async (dir) => {
  return mkdir(dir, { recursive: true });
});
```

After:

```ts
await pMap(
  [
    // `[...new Set()]` for unique paths
    ...new Set(
      files.outputFiles.map(({ path }) => {
        return dirname(path);
      }),
    ),
  ],
  async (dir) => {
    return mkdir(dir, { recursive: true });
  },
);
```

### Reduce complexity: Rule of least power

TODO: Examples

### Reduce complexity: Reorder code to avoid steps altogether

TODO: Examples

### Reduce complexity: Consider system changes when new work adds complexity

New work may seem to require a branch, flag, field, second test, or other structure. Before adding it, check whether a change elsewhere in the system would avoid the extra complexity. Existing fixtures, values, APIs, and control flow are not always fixed.

Before:

The existing student has no student assessment. Keeping that fixture requires another student and another test. Both tests also need longer names to distinguish them:

```diff
 const testStudent = {
   user: { email: testUsers.oliviaWagner.email, password: 'asdf' },
   enrollments: {
     pernExtensiveFlex: { rootSlug: 'pern-extensive-flex-winter-2025-eu' },
   },
 };

+const testStudentWithAssessment = {
+  user: { email: testUsers.lenaKovac.email, password: 'asdf' },
+  enrollments: {
+    pernExtensiveFlex: { rootSlug: 'pern-extensive-flex-winter-2025-eu' },
+  },
+  studentAssessment:
+    testStudentAssessments.lenaKovacPernExtensiveFlexWinter2025EuFinalAssessment,
+};
+
-test('PERN Extensive (Flex) student browses', async ({ page }) => {
+test('PERN Extensive (Flex) student browses course and appointments', async ({
+  page,
+}) => {
   // logs in, browses the course, a lecture and a project
 });
+
+test('PERN Extensive (Flex) student browses student assessment and certificate', async ({
+  page,
+}) => {
+  // logs in again, as a different student
+  // browses the student assessment and certificate
+});
```

After:

Another student in the same cohort has a student assessment. Switching the fixture to that student keeps one test, retains its original name, and lets the same flow continue through the assessment and certificate:

```diff
 const testStudent = {
-  user: { email: testUsers.oliviaWagner.email, password: 'asdf' },
+  user: { email: testUsers.lenaKovac.email, password: 'asdf' },
   enrollments: {
     pernExtensiveFlex: { rootSlug: 'pern-extensive-flex-winter-2025-eu' },
   },
+  studentAssessment:
+    testStudentAssessments.lenaKovacPernExtensiveFlexWinter2025EuFinalAssessment,
 };

 test('PERN Extensive (Flex) student browses', async ({ page }) => {
   // logs in, browses the course, a lecture and a project
+  // browses the student assessment and certificate
 });
```

The coverage requires one student with an assessment, not two students and two tests.

Consider before adding structure:

- Which existing choice requires this structure?
- Is that choice required?
- What depends on it?
- Would changing it leave the system simpler overall?

### Reduce complexity: Terse comments

Many code comments can follow a simple format of `<verb> <noun> <reason>` to reduce the cognitive load and effort of reading, understanding and maintaining them.

See the `code-comments` skill for action and fact forms, verbs to use for failures and long background comments.

### Reduce duplication: Avoid duplicated identifiers for same collection of objects

As code grows, it is common to accidentally keep multiple 1-to-1 collections that refer to the same underlying items (often an "intermediate" list like nodes or paths plus a "derived" list like images, titles, or hrefs), which quietly creates a synchronization obligation and makes later changes brittle.

The fix is to step back, look at what the whole program truly needs to do with those items, normalize to one canonical representation for that collection, and derive any alternate views (filters, projections, lookups) at the point of use instead of storing them as separate long-lived arrays / sets / objects / maps.

Exceptions:

- Short-lived denormalization for performance or data access patterns

#### Example 1: HTML extraction `lis` + `imgs` (1-to-1)

Before:

```ts
const lis = selectAll('li.meme');
const imgs = lis.map((li) => {
  return li.querySelector('img')!;
});

const memes = imgs.map((img, index) => {
  return {
    src: img.getAttribute('src')!,
    caption: lis[index]!.querySelector('.caption')?.textContent?.trim() || '',
  };
});
```

After:

```ts
const memes = selectAll('li.meme').map((li) => {
  return {
    src: li.querySelector('img')!.getAttribute('src')!,
    caption: li.querySelector('.caption')?.textContent?.trim() || '',
  };
});
```

#### Example 2: Crawl/index `urls` + `htmlPages` (1-to-1)

Before:

```ts
const urls = getUrls();
const htmlPages = await Promise.all(
  urls.map((url) => {
    return fetchHtml(url);
  }),
);

const pagesToIndex = htmlPages.map((html, index) => {
  return {
    url: urls[index]!,
    html,
  };
});

for (const page of pagesToIndex) {
  indexPage(page.url, page.html);
}
```

After:

```ts
const pagesToIndex = await Promise.all(
  getUrls().map(async (url) => {
    return {
      url,
      html: await fetchHtml(url),
    };
  }),
);

for (const page of pagesToIndex) {
  indexPage(page.url, page.html);
}
```

#### Example 3: "Already processed" tracking (two arrays -> one array with `.processed`)

Before:

```ts
const paths = ['a.mdx', 'b.mdx', 'c.mdx'];
const processedPaths: string[] = [];

for (const path of paths) {
  await sync(path);
  processedPaths.push(path);
}
```

After:

```ts
const files = [
  { path: 'a.mdx', processed: false },
  { path: 'b.mdx', processed: false },
  { path: 'c.mdx', processed: false },
];

for (const file of files) {
  await sync(file.path);
  file.processed = true;
}
```

#### Example 4: Per-path derived data (paths + derived array -> one array of items)

Before:

```ts
const paths = [lecturePath, cheatsheetNextJsPath, projectFullPath];
const excerptsByPath: { path: string; excerpt: string }[] = [];

for (const path of paths) {
  const content = await readFile(path, 'utf8');
  const firstTenWords = content.match(/(\S+\s+){0,10}/)![0]!;
  excerptsByPath.push({
    path,
    excerpt: firstTenWords,
  });
}
```

After:

```ts
const files = [
  { path: lecturePath, excerpt: '' },
  { path: cheatsheetNextJsPath, excerpt: '' },
  { path: projectFullPath, excerpt: '' },
];

for (const file of files) {
  const content = await readFile(file.path, 'utf8');
  const firstTenWords = content.match(/(\S+\s+){0,10}/)![0]!;
  file.excerpt = firstTenWords;
}
```

#### Example 5: Derive ids directly instead of storing intermediate arrays

Before:

```ts
const activeUsers = users.filter((user) => {
  return user.isActive;
});

const activeUserIds = activeUsers.map((user) => {
  return user.id;
});
```

After:

```ts
const activeUserIds = users
  .filter((user) => {
    return user.isActive;
  })
  .map((user) => {
    return user.id;
  });
```

### Reduce duplication: Consolidate similar functions

TODO: Examples

### Reduce duplication: Merge duplicate code (but avoid creating functions)

TODO: Examples
