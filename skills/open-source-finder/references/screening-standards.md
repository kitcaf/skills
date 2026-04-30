# Screening Standards

Use these standards to decide whether a GitHub project is suitable for sustained open-source contribution and learning.

Before screening projects, retrieve candidates using `github-retrieval.md`. Screening should be based on current GitHub repository data, README/docs inspection, and repository structure, not memory or popularity alone.

## Core Standard

Prioritize:

```text
project value + self-improvement + fit
```

Stars, issues, PRs, and recent activity are supporting evidence only. They must not dominate the recommendation.

## Project Value

A valuable project solves a real problem and has a believable reason to exist.

Positive signals:

- The README clearly explains what the project does, who it helps, and what problem it solves.
- The repository is a real tool, library, SDK, framework, service, plugin, CLI, or infrastructure component.
- The project belongs to a technical direction that is still useful or growing.
- There are signs of real use, such as stars, forks, package downloads, external mentions, ecosystem integrations, or user questions.
- The codebase has structure, releases, examples, docs, tests, or a versioned package.
- The license is clear enough for normal open-source contribution.

Negative signals:

- The repository is mainly a concept demo, tutorial dump, or one-off experiment.
- The README is vague, hype-driven, or cannot explain the actual use case.
- The project only matches the user's direction by keyword, not by substance.
- The project appears to have no real users and no clear future utility.

## Self-Improvement

The project should help the user learn the direction they care about.

Positive signals:

- The code contains real engineering content in the user's target area.
- The repository is more than a single-file demo but still understandable.
- The user can learn architecture, testing, API design, documentation, integration work, or domain-specific implementation details.
- There are modules, examples, docs, tests, integrations, or small bugs that can become first contribution paths.
- Contribution can start shallow and gradually become deeper.

Direction-specific examples:

- For AI Agent: workflow orchestration, tool calling, memory, RAG, evaluation, multi-agent coordination, LLM app infrastructure, observability.
- For Rust: async, CLI design, parsers, networking, storage, wasm, error handling, testing, crate design.
- For frontend tooling: bundling, components, monorepos, design systems, test infrastructure, developer experience.
- For backend: APIs, databases, queues, auth, deployment, reliability, tests, service boundaries.

Negative signals:

- The project is too trivial to teach meaningful engineering.
- The interesting parts are hidden behind external services or closed systems.
- The user would mostly make typo fixes without a path to deeper work.
- The project is so complex that the learning curve blocks practical contribution.

## Fit

Fit means the project is appropriate for this user to contribute to over weeks or months.

Positive signals:

- Stars are preferably in `100..5000`; `300..2000` is often the strongest band.
- The repository is medium-sized: substantial enough to learn from, small enough to understand.
- The technical stack is close to the user's current ability but slightly challenging.
- The code structure has understandable boundaries.
- Local setup appears feasible.
- Documentation is imperfect but not absent.
- The project is not so mature that only deep internal bugs remain.
- There are contribution entry points: docs, examples, tests, small bugs, integrations, adapters, config docs, or repeated user questions.

Negative signals:

- The repository is extremely large, mature, or process-heavy.
- The project has many issues and PRs because it is already a large maintenance machine.
- The project is too small, inactive, or personal to support sustained learning.
- The documentation is completely missing, making onboarding too expensive.
- The project requires costly infrastructure or domain access just to run.
- Maintainers appear completely unreachable.

## Weak Signals

Use these only as small supporting signals:

- Recent commits or releases
- Issue replies
- Merged PRs
- External contributors
- `good first issue` or `help wanted` labels

Interpretation:

- Some recent interaction can increase confidence.
- No recent activity can increase risk but should not automatically reject a strong fit.
- Many issues or PRs can indicate maturity, noise, or high contribution difficulty.
- Small external PRs being accepted is more meaningful than raw PR volume.
- Friendly maintainer review behavior is more useful than merge count alone.

## Recommendation Rule

Recommend a project only when:

- It has clear project value.
- It can teach the user something aligned with their stated direction.
- Its size, maturity, and contribution surface make sustained contribution realistic.

Do not recommend projects merely because they are popular, highly active, or have many issues.
