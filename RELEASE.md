# Release Workflow

Use this checklist when cutting a new `action-gh-release` release.

## Inputs

- Decide the semantic version bump first: `major`, `minor`, or `patch`.
- Review recent merged PRs and labels before drafting the changelog entry.
- Make sure `master` is current and the worktree is clean before starting.

## Checklist

1. Update [package.json](package.json) to the new version.
2. Add the new entry at the top of [CHANGELOG.md](CHANGELOG.md).
   - Summarize the release in 1 short paragraph.
   - If the summary mentions issues, use plain `#123` references or full issue links; do not wrap issue numbers like `#123` in backticks.
   - Prefer user-facing fixes and features over internal churn.
   - Keep the merged PR list aligned with `.github/release.yml` categories.
3. Run `pnpm install` to refresh [pnpm-lock.yaml](pnpm-lock.yaml).
4. Run the full local verification set:
   - `pnpm run validate`
   - `pnpm run build`
5. Commit the release prep.
   - Use a plain release commit message like `release 3.0.0`.
6. Create the annotated tag for the release commit.
   - Example: `git tag -a v3.0.0 -m "v3.0.0"`
7. Push the commit and tag.
   - Example: `git push origin master && git push origin v3.0.0`
8. Create the GitHub release from the new tag.
   - Prefer the release body from [CHANGELOG.md](CHANGELOG.md), then let GitHub append generated notes only if they add value.
   - Publishing the release triggers [`.github/workflows/release.yml`](.github/workflows/release.yml).

## Automated Release Workflow

When a GitHub release is published, the release workflow:

1. Checks out the release tag.
2. Installs dependencies and builds `dist/index.js`.
3. Attests `action.yml` and `dist/index.js` with build provenance.
4. Uses [build-and-tag-action](https://github.com/iShark5060/build-and-tag-action) to push the built bundle to the release tag and update floating major/minor tags (for example `v3`, `v3.0`).
5. Pushes a `<tag>-src` tag pointing at the source commit on `master`.

Draft and pre-releases still update the release tag, but floating major/minor tags are skipped for those release types.

## Notes

- Behavior changes should already have matching updates in [README.md](README.md), [action.yml](action.yml), tests, and `dist/index.js` before release prep begins.
- Docs-only releases still need an intentional changelog entry and version bump decision.
- If a release is mainly bug fixes, keep the title and summary patch-oriented; do not bury the actual fixes under dependency noise.
- Keep `v2` pinned to the latest `2.x` release for consumers that still need the Node 20 runtime.
