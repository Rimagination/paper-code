<div align="center">
  <img src="./assets/logo.png" alt="paper-code" width="96" />
  <h1>paper-code</h1>

  <a href="./skills/paper-code/SKILL.md"><img alt="Agent Skill" src="https://img.shields.io/badge/agent%20skill-4%20markdown%20files-0F766E?style=flat-square" /></a>
  <a href="./LICENSE"><img alt="License" src="https://img.shields.io/badge/license-MIT-0F766E?style=flat-square" /></a>

  It only moves when you name it. Hand your research code to the agent: it comes back readable and checkable by peers, with the computed behaviour verified unchanged.

  <sub><a href="README.md">简体中文</a> · <strong>English</strong></sub>
</div>

<p align="center">
  <a href="#what-is-this">What is this</a> ·
  <a href="#what-it-does">What it does</a> ·
  <a href="#getting-started">Getting started</a> ·
  <a href="#what-you-get">What you get</a> ·
  <a href="#boundaries">Boundaries</a> ·
  <a href="#further-reading">Further reading</a>
</p>

## What is this

**paper-code is an agent skill for tidying research code.** Give it an analysis script, a notebook or a research repository; it first writes down a checkable chain of computation, then removes reading obstacles with the smallest necessary edits, compares behaviour before and after, and hands back a change summary, a verification record and a list of open questions.

It starts only on explicit invocation: `/paper-code`, your host's skill picker, or a clear instruction to use paper-code on this task. Once started it picks one of three modes by intent: audit (read-only), polish (the default tidy-up), release (reproduction bundle).

> No paper is fine: the method mapping is then labelled "no paper provided, not checked" — it will not invent equations, units or table numbers.

### Who it is for

- Researchers and students preparing code for submission or sharing, who need peers to read and reproduce it.
- Authors, collaborators and reviewers who need confirmation that tidying did not change the scientific result.
- Labs wiring research-code review into an AI agent workflow.

If what you want is a new algorithm, a different statistics library, vectorization or a fix to a research error, raise that as its own task; paper-code's polish never smuggles in method changes. An ordinary "improve readability" request does not activate it either.

### Verified facts

- The package is exactly 4 markdown files: 79 lines of main rules plus three references loaded on demand.
- 5 differential tests pass on a synthetic paired-observation fixture, including 4,369 enumerated datasets over a finite input space.
- 31 static scenarios were walked through by an independent reviewer who read the whole skill.
- Not measured: token or latency benchmarks, human-comprehension studies, end-to-end runs on real research repositories, cross-host auto-invocation behaviour.

## What it does

| Task | Example input | Result |
|---|---|---|
| **Read-only audit** | "show me what's hard to read, don't change anything" | A list of reading obstacles and method doubts; no file edits, no project-code execution. |
| **Minimal polish** | an analysis script or notebook | Necessary edits to names, comments and local structure, with a baseline comparison and a verification level. |
| **Reproduction bundle** | a code directory headed for submission | Navigation, run commands and verification status on top of the polish; nothing published or uploaded. |
| **Behaviour check** | the same inputs before and after | Differential comparison against pre-declared tolerances; coverage labelled honestly as V0–V3. |
| **Doubts listed separately** | a suspected statistical-definition or unit error | Location, consequence and an author-to-confirm item; never mixed into the polish. |

Each reading obstacle stops at the first action that solves it: if nothing blocks understanding, nothing changes; a clear loop is not compressed into one line; a simple script does not grow a package architecture.

## Getting started

### Agent skill

Send this sentence to Codex, Claude Code, Cursor, Qoder, ZCode or a similar agent:

```text
Install this skill for me: https://github.com/Rimagination/paper-code
```

After installing, restart the session or run `/skills reload`, then invoke it by name, for example:

```text
/paper-code tidy this analysis script: baseline first, then edit, then tell me what you verified.
```

For a review only, say "use paper-code to audit this code read-only" and it will not touch a file.

## What you get

- **What changed:** file links, the concrete reading benefit, the important behaviours preserved; if nothing changed, why.
- **What was verified:** the commands and methods actually run, comparison results, criteria and coverage; static-only work is labelled V0.
- **What remains unconfirmed:** paths not run, integration risk, method questions or submission items waiting on you.
- **Unchanged contracts:** returned keys, CLI, defaults, exceptions, paths and the scientific meaning of figures stay as they were.
- **References on demand:** scientific invariants and the verification protocol load only the sections involved, not a literature review per tidy-up.

## Boundaries

- Without an explicit call it does not start; a mistaken load stops at the invocation gate, reading no references and running no extra checks.
- It does not assume AI-written code is worse, and never rewrites by line count, "AI smell" or personal taste.
- Fixing methods, vectorizing, parallelizing, swapping libraries, changing precision or random streams are separate decisions needing separate authorization.
- Paths with a failed baseline or missing dependencies, data or compute fall back to read-only audit; independent components may continue, and integration gaps are stated.
- It never updates golden files, loosens assertions or deletes failing tests to turn checks green; while a new difference is unresolved it stops widening the change.
- A finite regression is not equivalence over arbitrary inputs; you get no "ready to submit" certificate.
- No automatic commit, push, publish or upload; licensing, attribution and AI-use disclosure remain the authors' decisions.
- Without a named journal it applies generic reproducibility checks only; with one, its current rules still need verifying at that moment.

## Further reading

- [Main rules, SKILL.md](skills/paper-code/SKILL.md): invocation gate, modes, boundaries and delivery format.
- [Scientific invariants](skills/paper-code/references/scientific-invariants.md): samples, statistics, numerics, randomness, space-time, contracts, figures, notebooks.
- [Verification protocol](skills/paper-code/references/verification.md): baselines, comparison definitions, V0–V3, and what to do when you cannot run.
- [Evidence](skills/paper-code/references/evidence.md): an evidence matrix over 7 sources with applicability notes.
- [Issues](https://github.com/Rimagination/paper-code/issues): report a problem or request a feature.
- License: [MIT](LICENSE).
