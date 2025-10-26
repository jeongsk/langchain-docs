---
name: Find Untranslated Docs
description: Finds markdown/MDX files that need Korean translation. Use when searching for documentation files that haven't been translated to Korean yet, or when the user asks to find files needing translation.
---

# Find Untranslated Docs

Identifies all markdown and MDX documentation files under a given path that contain no Korean characters and therefore need translation.

## Instructions

When a user provides a directory path to check for untranslated files:

1. **Find all markdown files** recursively under the given path:
   - Use Glob tool with patterns `**/*.md` and `**/*.mdx`
   - Search under the provided path

2. **Exclude auto-generated files**:
   - Skip files in `**/reference/**` directories (API reference docs)
   - Skip files in `node_modules/`, `.git/`, or other dependency/hidden directories

3. **Check for Korean characters**:
   - Use Grep tool with pattern `[ㄱ-힣]` to find files containing Hangul
   - Set output_mode to `files_with_matches` to get list of files WITH Korean text
   - Files NOT in this list are the ones needing translation

4. **Present results** in this format:

```
Found X markdown files needing Korean translation under [path]:

1. path/to/file1.mdx
2. path/to/file2.md
3. path/to/file3.mdx
...

Total: X files need translation
```

If all files already contain Korean text:
```
✓ All markdown files under [path] already contain Korean text.
No translation needed.
```

## Implementation Strategy

**Step 1**: Use Glob to find all markdown files
```
Pattern: **/*.md and **/*.mdx under the user-provided path
```

**Step 2**: Use Grep to find files WITH Korean characters
```
Pattern: [ㄱ-힣]
Output mode: files_with_matches
Path: same as step 1
```

**Step 3**: Compare the two lists
- Files found by Glob but NOT found by Grep need translation
- Exclude any files in `**/reference/**` paths

**Step 4**: Report findings with file counts and clear formatting

## Example Usage

**User asks**: "Find untranslated files in src/docs/guides"

**You do**:
1. Glob for `src/docs/guides/**/*.{md,mdx}`
2. Grep for `[ㄱ-힣]` in those files
3. Identify files with no Korean characters
4. Present the list

## Best Practices

- Use parallel tool calls when possible (Glob and Grep can run simultaneously)
- Provide clear, actionable output with file counts
- Use relative paths from the project root for easy copy-paste
- Group results logically if there are many files
- Exclude reference docs automatically without mentioning unless relevant

## Notes

- Files with even a single Korean character will NOT be reported (they're considered "in progress")
- Empty files will be reported as needing translation
- This checks for presence of Hangul only - it doesn't validate translation quality
