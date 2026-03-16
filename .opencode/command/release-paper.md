---
name: release-paper
description: Refresh release diff, verify build, commit, and tag a new paper release
---

You are a release assistant for this paper repository.

The user provides exactly one argument:

- `$ARGUMENTS`: the new release tag to create, for example `v1.20`

Your job is to update the release diff for `tex/index.tex`, verify it builds locally, commit the diff file, and then create the requested tag.

## Repository-Specific Paths

- Source paper: `tex/index.tex`
- Release diff source: `tex/index-release-diff.tex`
- Release diff PDF target: `tex/index-release-diff.pdf`

## Required Workflow

1. Validate input.
   - Fail if no tag argument is provided.
   - Treat the provided argument as the new release tag.

2. Discover the base tag automatically.
   - Use the latest existing git tag in the repository as the base tag.
   - If no tag exists, stop and explain that a base tag is required.

3. Run safety checks before changing anything.
   - Verify the base tag exists.
   - Verify the requested new tag does not already exist.
   - Check the current git status and identify unrelated changes.
   - Do not revert or disturb unrelated user changes.

4. Regenerate the release diff.
   - Compare the latest tag against the current `HEAD` version of `tex/index.tex`.
   - Write the diff to `tex/index-release-diff.tex`.
   - Label the diff so the header clearly shows the base tag and `HEAD`.
   - Use the same latexdiff approach that works in this repository.

5. Verify the diff build locally before any commit or tag.
   - Build `tex/index-release-diff.tex` with `latexmk -g -pdf` from the `tex/` directory.
   - If the build fails, stop immediately.
   - Do not commit or create a tag unless the build succeeds.

6. Commit only the release diff file.
   - Stage only `tex/index-release-diff.tex`.
   - Do not stage unrelated files.
   - Use this commit message format:
     - `ci: refresh <base-tag> release diff`

7. Create the new annotated tag.
   - Create an annotated git tag using the user-provided new tag.
   - Use the tag message equal to the tag name.

8. Verify and report.
   - Show the resulting commit hash.
   - Confirm the new tag exists.
   - Report any remaining uncommitted or untracked files.

## Execution Notes

- Prefer repository commands that were already proven to work in this repo.
- Keep the workflow strict: diff generation -> local build passes -> commit -> tag.
- Never create the tag before the commit succeeds.
- Never commit if the diff build fails.
- Never use destructive git commands.
- If the repository contains unrelated dirty files, leave them untouched and commit only the diff file.

## Expected Outcome

After successful execution:

- `tex/index-release-diff.tex` is updated against the latest existing tag
- the diff PDF has been verified locally
- a commit for the diff file exists
- the requested annotated release tag exists
