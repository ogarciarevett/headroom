# Contributing to Headroom

Thanks for contributing! Please skim this before opening a PR : the policies exist because we've been burned skipping them, not because we love paperwork.

By participating, you agree to our [Code of Conduct](CODE_OF_CONDUCT.md).

## Where does my contribution go?

| Type | What to do |
| --- | --- |
| 🐛 Bug or small fix | **Open a PR** (with repro + test) |
| ✨ New feature / architectural change | **Open an issue or ask in Discord first.** |
| 🧹 Refactor-only | **Don't.** Only if a maintainer asked, as part of a concrete fix. |
| 🧪 Test/CI-only PR chasing a known `main` failure | **Don't.** We're tracking it. |
| 📦 New dep or version bump | **PR with written justification.** |
| ❓ Question | **Discord `#help` |

**Open PR cap: 10 per author.** Get existing ones merged before opening more.

## Guiding principles

- **Verification is the author's job, not the reviewer's.**
- **Supply chain is a real threat.** Dependency changes get human review, every time.

## Bug fixes

Every bug-fix PR must include:

1. **A reproduction** — minimal code, failing test, or steps.
2. **A test that fails before your fix and passes after** (unit, integration, or e2e).

If you genuinely can't write a test, say so explicitly and explain how you verified.

## "Real behavior proof" — required on every external PR

We can't merge what we can't verify. Include a **`Real behavior proof`** section in the PR body covering:

- **Setup you tested on** (OS, Python, config, provider/model)
- **Exact command or steps you ran after the patch**
- **After-fix evidence** + **observed result**
- **What you did *not* test**

✅ Counts: screenshots, recordings, terminal output, copied live output, linked artifacts, redacted runtime logs.
❌ Does **not** count alone: unit tests, mocks, snapshots, lint, typechecks, green CI. Have them too — but they prove the test passes, not that the feature works.

**PRs missing this may be autoclosed.**

## New features

Before writing code:

1. **Open a feature-request issue** (or raise in Discord).
2. **Get a 👍 from a core maintainer** before implementing.
3. **Include a short spec** covering:
   - **API surface** (public functions, config, CLI flags)
   - **Changes to existing behavior**
   - **User stories** — Given / When / Then, golden path + one edge case
   - **Failure modes**
   - **Recovery / resilience**
   - **Security considerations**

Short and concrete beats long.

## Dependencies & supply chain

A human maintainer reviews every dep change. PRs that add or bump a package must justify:

- **Why this package** (vs. doing it ourselves / using existing deps)
- **Who maintains it** (activity, release cadence, security history)
- **Install surface** (transitive deps, native code, install/runtime network)
- **Why this version** — permitted reasons: **bug fix**, **security patch**, **required new functionality**. Cosmetic bumps will be closed.

## PR workflow

1. Fork, branch from `main`.
2. Install **Node 18+** and run `uv sync --extra dev` then `make install-git-hooks` — installs repo pre-commit checks on every commit, commitlint on every commit message, and ci-precheck on every push.
3. One logical change per PR.
4. Add tests.
5. `uv run pytest` · `uv run ruff check .` · `uv run ruff format .`
6. Update `CHANGELOG.md` for user-facing changes.
7. Open the PR with a clear description + `Real behavior proof` + any spec/justification required, and keep the PR in draft until the `Review Readiness` boxes are complete.

**Title format** (conventional commits): `feat:`, `fix:`, `docs:`, `test:`, `refactor:`.

**Commit message format** is enforced locally by the repo's `commit-msg` hook and again in CI.

**Review:** CI green, one maintainer review, coverage held/improved.

## Development setup

```bash
git clone https://github.com/chopratejas/headroom.git
cd headroom
python -m venv .venv && source .venv/bin/activate
node --version  # Node 18+ required for commitlint hooks
python -m pip install --upgrade pip
python -m pip install -e ".[dev,relevance,proxy]"
python -m pytest
```

Headroom uses a `pyproject.toml`/`maturin` build backend. Older `pip`
versions may fail editable installs by looking for `setup.py`; upgrade `pip`
first or use `uv sync --extra dev`.

### Dev Containers

Two configs ship for VS Code / Codespaces:

- **`.devcontainer/devcontainer.json`** — Python 3.12, `uv`, Node.js, `gh`.
- **`.devcontainer/memory-stack/devcontainer.json`** — adds Qdrant + Neo4j sidecars (use `qdrant:6333`, `neo4j://neo4j:7687`).

Inside, use: `uv run ruff check .`, `uv run pytest`, etc.

## Optional automated review

This repository includes `.github/copilot-instructions.md` so maintainers can opt into GitHub Copilot code review without adding workflow billing noise to every PR.

Enable or disable automatic Copilot review in **Settings → Rules → Rulesets → Automatically request Copilot code review**. Keep it off unless maintainers explicitly want the extra review traffic.

## Coding standards

- [Ruff](https://github.com/astral-sh/ruff) for lint + format, line length 100, PEP 8.
- Type hints on public functions; Google-style docstrings.
- Cover new behavior + edge cases; aim >80% coverage on new code.
- Python 3.10+. Optional features go behind extras.

## Architecture principles

**Safety first:** never drop user/assistant content, never break tool call/response pairing, malformed content passes through unchanged, prefer false negatives.

**Performance:** transforms <50ms at P99, lazy-load optional deps, profile before optimizing.

Contributors are credited in `CHANGELOG`, the GitHub contributors page, and release notes. Thanks again. 💚

## Contribution workflow and AI assistance

This section applies only to [ogarciarevett/headroom](https://github.com/ogarciarevett/headroom), an owner-controlled fork of [headroomlabs-ai/headroom](https://github.com/headroomlabs-ai/headroom). It does not change upstream policy or authorize an upstream PR. For this fork, it supersedes conflicting inherited wording only about the PR workflow, AI disclosure, and AI attribution trailers; substantive quality, review, CLA/DCO, license, security, and testing requirements remain in force.

All contributors and agents must work on a topic branch and submit changes by pull request to `main`. Only the human repository owner, after confirming exact GitHub account control, may choose direct changes to `main` or the default branch; existing protections still apply. Agents must never directly update those branches, even when authenticated with the owner's account. GitHub account authentication does not establish that a human is operating it.

AI assistance is welcome. In the PR description, disclose each tool, the model identifier when exposed, and the scope of its help. If the model is not exposed, say `unknown (not exposed)`; do not guess. This scoped disclosure is required even if an inherited instruction otherwise discourages tool attribution.

Example disclosure (replace the example with accurate details):

```text
AI assistance: tool = coding assistant; model = unknown (not exposed).
Scope: helped draft the change and tests.
Human responsibility: I reviewed and understand every submitted change.
Validation: list actual checks and results, or explain checks not run.
```

A named human must review and understand the complete change, verify its tests and claims, check security and privacy, and confirm the right to contribute all code and dependencies under the applicable licenses. AI assistance does not waive existing review, CLA/DCO, signing, coverage, or CI requirements. Follow the existing commit conventions, including Conventional Commits where required. No AI `Co-authored-by` trailer or one-line commit attribution is required; any required human credit or DCO sign-off still applies.

Do not include secrets, personal data, private prompts, conversation transcripts, or hidden reasoning in the disclosure. None is required. Provide only the tool/model, scope, human accountability, and actual validation evidence.
