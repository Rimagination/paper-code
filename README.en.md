<h1 align="center">paper-code</h1>

<p align="center">
  <em>Make the implementation readable by peers, not human-looking.</em>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/explicit%20invocation-only-111111?style=flat-square" alt="Runs only on explicit invocation">
  <img src="https://img.shields.io/badge/4%20markdown%20files-no%20new%20deps-111111?style=flat-square" alt="4 markdown files, no new dependencies">
  <img src="https://img.shields.io/badge/verification%20levels-V0%E2%80%93V3-111111?style=flat-square" alt="Verification levels V0-V3">
  <img src="https://img.shields.io/badge/license-MIT-111111?style=flat-square" alt="MIT license">
  <img src="https://img.shields.io/badge/benchmark-none%20measured-111111?style=flat-square" alt="Benchmark: none measured">
</p>

<p align="center">
  <sub><a href="README.md">简体中文</a> · <strong>English</strong></sub>
</p>

---

You know this person. A week before submission they read your two-hundred-line script of `a`, `b`, `c`, pointed out that the denominator on line 37 is n, not n−1, and then changed almost nothing for you — the names, the comments, the reading order. The science stayed yours to decide.

paper-code puts that person inside your agent.

It assumes AI-written research code is **not inherently worse**, and it never rewrites by line count, "AI smell" or personal taste. It does two things: removes real reading obstacles, and checks that the computed behaviour did not change.

## Numbers

Only what has actually been verified.

| Item | Measured |
|---|---|
| Skill package | 4 markdown files; `SKILL.md` body 79 lines / 3,324 characters; 10,659 characters total |
| Optimization delta | main file 4,374 → 3,324 characters (−24%), whole package 11,018 → 10,659 characters |
| Package integrity | 4 markdown files with recorded SHA-256; the local source and shared install copies are byte-identical |
| Scenario walkthrough | 31 static scenarios (10 targeting this optimization), judged by an independent reviewer reading the whole skill |
| Regression fixture | 5 differential tests pass (0.607s), including 4,369 enumerated datasets over a finite input space |
| Loading | references are read per risk and per section; a confirmed-inert comment change is labelled V0 with no numerical rerun |

**The rule was never "make it look human-written."** It is: a reader can locate the computation steps, understands each input and output, knows why a parameter has its value, and can check the result themselves. Anything not measured goes in [Limits and unverified claims](#limits-and-unverified-claims) instead of becoming a percentage.

## When it runs

Three ways only: `/paper-code`, your host's skill picker, or an explicit instruction to use paper-code on this task.

What does not run it: "clean up this code", "improve readability", "prepare this for submission", the name appearing in a file path or a tool output, or asking "what is paper-code", "change paper-code's rules", "don't use paper-code". If it gets loaded by mistake it stops at the first rule: no reference reads, no extra checks.

Within one authorized task, "continue" does not require naming it again. When the task ends, you cancel, or you switch to something unrelated, it stops applying — and nothing is written as a cross-task default.

This is a constraint at the skill layer. It does not claim any host has switched off automatic invocation.

## Before / after

A synthetic paired-observation analysis (not a real study, not a paper result):

```python
def analyze(rows):
    # Keep complete pairs. Zero is a real measurement.
    a = []
    for r in rows:
        if r[0] is not None:
            if r[1] is not None:
                a.append(float(r[1]) - float(r[0]))
    ...
    b = 0.0
    for x in a:
        b = b + x
    b = b / len(a)
```

After:

```python
def analyze(rows):
    """Complete pairs -> second-minus-first changes -> mean, sample SD, positive fraction."""
    # Only None excludes a pair. Zero is a real measurement.
    paired_changes = []
    for row in rows:
        if row[0] is not None and row[1] is not None:
            paired_changes.append(float(row[1]) - float(row[0]))
    ...
    # Accumulate in input order to preserve floating-point results.
    total_change = 0.0
    for change in paired_changes:
        total_change = total_change + change
    mean_change = total_change / len(paired_changes)
```

What changed is where a reader stalls: local names mapped to what they compute, the missing-value rule stated, the reason the accumulation order is preserved written down.

What did not change: the n − 1 denominator, the sequential accumulation order, the returned keys, the exception message, the CLI contract, and that line about zero being a real measurement.

Equivalence is not asserted in prose: the AST is compared after normalizing every local name, then 5 differential tests (4,369 enumerated datasets, float special values, CLI contract) run the original and the edited version side by side.

## Three modes

| Mode | What you say | What it does |
|---|---|---|
| audit | "where is this hard to read", "review only, don't touch it" | Static read-through; no project-code execution, no file or environment changes |
| polish | "tidy up this research code" (default) | Minimal necessary edits, verification chosen by the nature of the change |
| release | "prepare a reproduction bundle" | Adds navigation and run instructions on top of polish; nothing published or uploaded |

The table is not a keyword trigger and grants no extra permission. It asks only for what would change the outcome: target files, whether editing is allowed, where the paper is. No paper is fine — the method mapping is then labelled "no paper provided, not checked".

## The minimal-edit ladder

For each reading obstacle, stop at the first rung that solves it. You do not walk all five:

1. Not in the way of understanding — change nothing. Style edits with no nameable benefit are skipped.
2. Unclear terms — accurate local naming, or one short mapping to the paper's symbols. Public contracts untouched.
3. Unclear intent — add units, assumptions or reasons that have evidence; delete comments that contradict the code or restate it line by line. Reason unknown, write "author to confirm" — never invent the scientific justification.
4. Mixed-up steps — clean local structure and leftover exploration that has been verified as unused; keep warnings and audit trails.
5. Still hard to follow — only then split by computation role, or merge duplication that has been proven equivalent.

A simple script stays one script, a clear loop is not compressed into one line, and no framework, dependency, CLI or config layer appears that nobody asked for.

The ladder runs *after* understanding, never instead of it: first write a checkable chain such as "pair filtering → per-subject differences → sample statistics", bind it to real locations, then decide what to touch.

Never on the chopping block: sample and missing-value rules, statistical definitions, units and dtypes, evaluation order, random streams, train/test splits, the scientific meaning of figures, public APIs and exceptions. Fixing a method, vectorizing, parallelizing or swapping libraries are separate decisions — a suspected research error is reported on its own, not smuggled into polish.

## Verification by risk

| Kind of change | Minimum sufficient action | Label |
|---|---|---|
| Read-only audit | Static walkthrough; invariant sections only where a scientific definition is involved | No claim of execution |
| Prose confirmed to have no execution/tool effect | Compare against the implementation and the full diff | **V0** static review; behaviour not executed |
| Rename, extraction, merge or other executable change | Baseline first, then edit, then regression | **V1 / V2** with the real commands and tolerances |
| Paper tables/figures, formal reproduction | Two-way comparison: before/after and against the paper target | **V3** |
| Missing dependencies, data, compute, or a baseline that already failed | That path falls back to read-only audit; independent components may continue | Per-path status plus the integration gap |

One rule that gets missed: docstrings, type annotations, imports, decorators, doctests, shebang and coding declarations, `#SBATCH`-style scheduler comments are **not automatically inert text**. Reflection, tests, builds or artifacts may consume them. If you cannot confirm, treat the edit as executable.

Not acceptable as a way to turn checks green: updating golden files, loosening assertions, deleting failing tests, widening the tolerance after seeing a diff. While a new difference is unresolved, stop widening the change, revert only your own related edits, and keep the user's edits and existing results.

In a controlled replay, the same comment-only edit went through the pre-optimization flow, read two full references and ran two numerical comparisons; the optimized flow classified it as V0, reading nothing and rerunning nothing. The executable rename variant in the same replay still built a baseline and regressed. That is one observation, not a benchmark: total tool calls in that round went 19 → 21, so no overall speedup is claimed.

## Delivery is three parts

- **What changed**: file links, the specific reading benefit, which important behaviours were preserved; if nothing changed, why, and audit states "read-only audit, no files modified".
- **What was verified**: the commands and methods actually run, the comparison results, the criteria and the coverage; static-only work is labelled V0.
- **What remains unconfirmed**: paths not run, integration risk, method questions waiting on the author, submission items.

You will not get a "ready to submit" certificate. A finite regression is not proof of equivalence over all inputs.

## What's inside

```
skills/paper-code/
├── SKILL.md                       invocation gate, modes, boundaries, ladder, delivery
└── references/
    ├── scientific-invariants.md   scientific invariants, 9 domain sections
    ├── verification.md            baseline, comparison definitions, V0-V3, unrunnable edges
    └── evidence.md                evidence matrix over 7 sources
```

The invariants split into §1 samples/missing/pairs, §2 statistics, §3 numerics, §4 RNG and machine learning, §5 space-time and units, §6 contracts and tool consumers, §7 figures, §8 notebooks, §9 method doubts; the verification protocol into §1 baseline, §2 comparison definitions, §3 levels, §4 when you cannot run, §5 delivery notes, §6 records.

Loaded on demand: no literature review per tidy-up, and no automatic invocation of other skills. The README and LICENSE sit at the repo root, outside the package on purpose — the skill ships exactly those 4 markdown files.

## Install

The skill lives at `skills/paper-code/` in this repo. Pick either way.

**One shared copy across agents** (the `skills` CLI symlinks by default instead of copying):

```powershell
skills add Rimagination/paper-code --global --agent '*' --skill paper-code --yes
```

**Manual install**: clone the repo and copy the whole `skills/paper-code/` directory.

```bash
git clone https://github.com/Rimagination/paper-code.git
cp -r paper-code/skills/paper-code ~/.qoder/skills/
```

Swap `~/.qoder/skills/` for your host's directory — `~/.claude/skills/`, `~/.codex/skills/`, `~/.config/opencode/skills/`, `~/.zcode/skills/` — ending up with `<skills>/paper-code/SKILL.md`.

Then confirm the entry points at the same target:

```powershell
Get-Item "$env:USERPROFILE\.qoder\skills\paper-code" | Select-Object FullName, LinkType, Target
```

If Windows refuses symlinks, enable Developer Mode or use an elevated PowerShell; use `--copy` only when you actually want separate copies.

Hosts that only read a rules file can load `skills/paper-code/SKILL.md` directly: there is no executable code, no network calls, no config file. After installing, restart the session or run `/skills reload`, then invoke `/paper-code`.

## Usage

| You say | You get |
|---|---|
| `/paper-code` + "tidy this script" | polish: baseline → minimal edits → regression → three-part delivery |
| `/paper-code` + "just show me what's hard to read" | audit: read-only, nothing touched |
| "use paper-code to prepare reproduction notes" | release: navigation and commands added, nothing published |
| "improve readability of this code" | this skill does not run; normal task flow applies |

Invocation covers only the task it was given. Polishing paper prose, hiding the origin of generated material, and ordinary product development are outside this workflow.

## FAQ

**Will it jump in on its own and slow me down?**
No. Without an explicit call it does not start, and a mistaken load stops at the invocation gate instead of reading references or running checks.

**A one-line comment edit still needs a numerical rerun?**
No. A text change confirmed to have no execution or tool-consumer effect is V0: static review, behaviour not executed. If the effect cannot be confirmed, it is treated as an executable change.

**What if the original code is just wrong?**
Reported separately as a method issue, with location and consequence, and changed only after separate authorization. Fixes never ride along with polish.

**Will it vectorize the loop or swap the statistics library?**
Not unless you approve that as its own task. Evaluation order, types, indexing, exceptions and side effects are preserved by default.

**Does it work without a paper?**
Yes. The code still gets tidied; the method mapping is marked "no paper provided, not checked" instead of inventing equations or table numbers.

**Can it claim journal compliance?**
Without a named journal it applies generic reproducibility checks only. With one, its current rules still need verifying at that moment. Licensing, author attribution, AI-use disclosure and anonymous-review compliance belong to the authors and the venue.

**"No references found" means I can delete it?**
No. Also check the CLI, config strings, dynamic imports, notebooks, docs and external users.

**Is AI-written code worse?**
Not assumed, and not assumed better. Two questions decide everything: can a reader follow it, and did the behaviour change?

## Limits and unverified claims

Stated plainly:

- No human-comprehension study; no readability score, reading time, or speedup percentage.
- No token or latency benchmark; the comparison above was a single replay and total tool calls did not fall.
- No end-to-end run on a real research repository or real paper artifacts; verification uses the synthetic fixture.
- "Not auto-invoked" has not been tested across hosts; on the current host only the updated description was observed to take effect.
- Passing a finite input-space regression is not equivalence over arbitrary inputs.
- Journal and reproducibility requirements in the evidence matrix were collected around 2026-11 and must be re-checked per venue and per issue.

## License

[MIT](LICENSE). Use, modify and redistribute freely, keeping the copyright and permission notice.

The licence was chosen by the author; the skill itself does not decide attribution, AI-use disclosure or anonymous-review compliance for you.

---

<p align="center">
  <sub>He deleted the comments. He kept the denominator.<br><a href="README.md">简体中文</a> · <a href="README.en.md">English</a></sub>
</p>
