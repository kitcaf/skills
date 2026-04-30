# GitHub Retrieval

Use this guide to retrieve candidate repositories from GitHub before applying `screening-standards.md`.

## Source Priority

Use official GitHub sources first:

1. GitHub MCP, GitHub connector, or equivalent official GitHub integration if available.
2. GitHub CLI with `gh search repos` if `gh` is installed and usable.
3. GitHub REST API, especially `GET /search/repositories`.
4. GitHub web search pages.

Use general web search only as a fallback or supplement for external usage evidence, such as package downloads, blog mentions, docs, or ecosystem references.

## Query Construction

Convert the user's direction into several search lanes:

- Core topic lane: direct GitHub topics and canonical keywords.
- Ecosystem lane: language, framework, package manager, runtime, or toolchain terms.
- Use-case lane: the problem the user wants to learn or contribute to.
- README lane: keywords that may appear in README content even when topics are sparse.

Prefer repository searches with:

```text
stars:100..5000 archived:false
```

Use `300..2000` as a stronger discovery band when results are too broad. Do not force `pushed`, issue, or PR recency qualifiers unless the user explicitly asks for active projects.

Useful GitHub search qualifiers:

```text
topic:<topic>
language:<language>
stars:100..5000
archived:false
fork:false
in:name,description,readme
good-first-issues:>0
help-wanted-issues:>0
license:<license-keyword>
```

Treat `good-first-issues` and `help-wanted-issues` as optional discovery aids, not quality proof.

## GitHub CLI Examples

When `gh` is available, prefer JSON output so candidates can be compared consistently:

```powershell
gh search repos "ai agent in:name,description,readme stars:100..5000 archived:false fork:false" --limit 30 --json fullName,description,url,stargazersCount,forksCount,language,license,updatedAt,pushedAt,isArchived,openIssuesCount
```

```powershell
gh search repos --topic=ai-agent --stars "100..5000" --archived=false --include-forks=false --limit 30 --json fullName,description,url,stargazersCount,forksCount,language,license,updatedAt,pushedAt,isArchived,openIssuesCount
```

For PowerShell queries containing negative qualifiers such as `-topic:example`, follow `gh search --help` for escaping rules.

## REST API Examples

Use the repository search endpoint when direct API access is available:

```http
GET https://api.github.com/search/repositories?q=topic:ai-agent+stars:100..5000+archived:false+fork:false&sort=stars&order=desc&per_page=30
Accept: application/vnd.github+json
```

For each promising candidate, inspect details and README:

```http
GET https://api.github.com/repos/OWNER/REPO
GET https://api.github.com/repos/OWNER/REPO/readme
GET https://api.github.com/repos/OWNER/REPO/contents
```

If authenticated access is available, use it to reduce rate-limit risk. Public repository search and public README reads can still work without authentication, but unauthenticated search is more limited.

## Candidate Collection Process

1. Run at least 3-5 different query lanes for each user direction.
2. Collect 20-50 raw candidates when possible.
3. Deduplicate by `owner/repo`.
4. Remove archived repositories, forks unless the fork itself is the real project, pure demos, and repositories clearly outside the user's direction.
5. Open or fetch README for each serious candidate.
6. Inspect repository structure lightly: docs, examples, tests, packages, modules, contribution files, and release/package indicators.
7. Check issues and PRs only as weak signals:
   - Look for maintainer responsiveness or complete silence.
   - Look for small external PRs being accepted when easy to verify.
   - Treat large issue/PR volume as possible maturity or noise, not automatic health.

## Minimum Evidence Per Recommendation

Only recommend a project after verifying:

- Link, name, stars, and archived status from GitHub.
- README or description explains the project clearly enough.
- The repository's substance matches the user's direction.
- The project has plausible learning value.
- The repository size or maturity is not obviously too large or too trivial.

## Output Discipline

Do not show raw queries or API details in the final answer unless the user asks. Use retrieval evidence to support the concise `Why it fits` explanation.

