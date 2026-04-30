---
name: open-source-finder
description: Find GitHub open-source projects for sustainable contribution and learning. Use when the user wants project recommendations for contributing to open source in one or more technical directions, especially when they want medium-sized, valuable, non-overmature repositories that match their growth goals.
---

# Open Source Finder

## Purpose

Find open-source projects that are worth the user's time to contribute to. Prioritize **project value + self-improvement + fit** over surface activity metrics.

Use live GitHub research before recommending projects. Repository data changes often, and stale recommendations are not acceptable.

## Input Handling

Accept one or more open-source directions from the user, such as `AI Agent`, `Rust CLI`, `frontend tooling`, `database tools`, `DevOps`, `observability`, `RAG`, or `Python web`.

If the user provides only directions, proceed with reasonable assumptions:

- Treat the user as someone looking for projects they can contribute to sustainably.
- Prefer repositories that are useful, learnable, and not too mature.
- Prefer projects where contribution can start from docs, examples, tests, small bugs, integrations, or understandable modules.

Ask a short clarification only when the direction is too broad to search meaningfully.

## Search Workflow

1. Expand the user's direction into related GitHub keywords, topics, ecosystems, and languages.
2. Use the official GitHub retrieval workflow in `references/github-retrieval.md` to find repository candidates.
3. Prefer repositories around `100..5000` stars as a core size signal. The best band is often `300..2000` stars.
4. Exclude archived repositories, pure demos, abandoned-looking toy projects, and projects whose README cannot explain what the project does.
5. Evaluate candidates using the standards in `references/screening-standards.md`.
6. Recommend only projects where all three core dimensions are credible:
   - Project value
   - Self-improvement
   - Fit

Only use general web search as a fallback or supplement when official GitHub search, GitHub API, GitHub CLI, or a GitHub connector is unavailable or insufficient.

## Ranking Principles

Use this internal ranking idea:

```text
recommendation value = project value x self-improvement x fit
```

Do not rank by stars alone. Do not treat issue count, PR count, or merged PRs as proof of community health or project value.

Use PRs, issues, recent commits, releases, and maintainer replies only as weak signals:

- They may indicate some activity or external interaction.
- Too many issues or PRs may indicate a mature, noisy, or hard-to-enter project.
- Lack of recent activity is a risk signal, not an automatic rejection.

## Output Format

Return a concise project list. Do not include scoring tables, long contribution plans, GitHub query logs, or broad methodology unless the user asks.

For each project, output:

```text
1. Project name
   Link:
   Introduction:
   Why it fits:
```

In `Why it fits`, briefly cover:

- The project's real value
- What the user can learn from it
- Why its size or maturity is suitable for sustained contribution

Prefer 5-10 recommendations unless the user asks for a different number.
