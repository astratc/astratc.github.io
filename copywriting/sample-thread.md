# Sample 2: X thread, launch of "lintlock" (fictional open-source tool)

1/ Today we're open-sourcing lintlock: a tiny CLI that stops a PR from merging when its dependency lockfile changed without a matching manifest change. 🧵

2/ The problem: lockfile drift. Someone runs install with a different version, 400 lines change, and reviewers skim past it. That's how surprise upgrades ship.

3/ lintlock compares the manifest and lockfile diffs in CI and fails with a one-line explanation: "lockfile changed, package.json didn't, run `npm ci`."

4/ It's ~300 lines, has no runtime dependencies, and supports npm, pnpm, yarn, cargo and poetry.

5/ Setup is one step in GitHub Actions:
`- uses: example/lintlock@v1`

6/ It's MIT-licensed, issues and PRs are welcome, and the roadmap is in the README.

7/ If lintlock saves you one confusing review, star the repo. That's how we know to keep going. → github.com/example/lintlock

**Hook variants:** "Your lockfile changed 400 lines and nobody noticed." / "A 300-line CI check for the PRs reviewers skim."
