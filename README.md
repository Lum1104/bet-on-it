<h1 align="center">bet-on-it</h1>

<p align="center">
  <strong>Predict first. Explain later.</strong>
  <br />
  Make coding agents put a falsifiable prediction on the record before a meaningful change.
</p>

<p align="center">
  <a href="https://github.com/Lum1104/bet-on-it/stargazers"><img src="https://img.shields.io/github/stars/Lum1104/bet-on-it?style=flat-square" alt="GitHub stars" /></a>
  <a href="./SKILL.md"><img src="https://img.shields.io/badge/agent%20skill-SKILL.md-111111?style=flat-square" alt="Agent Skill" /></a>
  <a href="./LICENSE"><img src="https://img.shields.io/github/license/Lum1104/bet-on-it?style=flat-square" alt="MIT license" /></a>
</p>

<p align="center">
  <a href="#install">Install</a> ·
  <a href="#before--after">Before / After</a> ·
  <a href="#the-core-mechanism">How it works</a> ·
  <a href="SKILL.md">Read the skill</a>
</p>

---

Coding agents are excellent at explaining a result after they have seen it.

That becomes a problem when the explanation quietly changes to fit the evidence. A speculative edit survives, a weak hypothesis sounds confirmed, and the next change builds on a story that was never actually tested.

`bet-on-it` makes the agent state what it expects *before* the experiment, then settle that bet against what happened.

Use `bet-on-it` before an action to commit to a prediction. Use [`prove-me-wrong`](https://github.com/Lum1104/prove-me-wrong) once a claim or fix is favored to design an attack that could defeat it.

## Install

Install with the [Skills CLI](https://www.skills.sh/docs/cli):

```bash
npx skills add Lum1104/bet-on-it
```

Install globally for all projects:

```bash
npx skills add Lum1104/bet-on-it -g
```

Then invoke it before a diagnostic or behavior-changing step:

```text
Use $bet-on-it to debug these duplicate checkout requests.
```

## Before / after

### Before

> The duplicate requests were probably caused by a React effect. I changed its dependencies. Tests pass, so that was likely the issue.

The explanation appeared after the edit. There is no record of what the agent expected to observe, and the change can remain even if the duplicate originated on the server.

### After

```text
Hypothesis: A React effect binds the submit handler twice after rerender.
Expected observation: Instrumentation records two handler calls per click.
Expected change: Correcting the effect produces one client request.
Disproof: One client call still produces two server writes.
```

Instrumentation records one client call and two server writes. The bet **failed**. The agent reverts the effect edit and tests a retry/idempotency hypothesis next.

## The core mechanism

### 1. Record the bet

Before a meaningful experiment or change, write four short fields:

```text
Hypothesis: <current causal explanation>
Expected observation: <specific result expected next>
Expected change: <files, state, metric, or behavior expected to change>
Disproof: <result that would make the hypothesis untenable>
```

A useful prediction names a value, error, event, diff, or behavior. “This should help” is not a bet.

### 2. Run the smallest discriminating step

Choose the cheapest safe action that separates the current hypothesis from a plausible alternative. Avoid bundling unrelated edits; an ambiguous diff produces an ambiguous result.

### 3. Settle it without rewriting it

Preserve the original prediction and append:

```text
Observed: <what actually happened>
Verdict: matched | partially matched | failed | inconclusive
Next action: <continue, refine, abandon, revert, or gather a better signal>
```

| Verdict | Required response |
| --- | --- |
| **Matched** | Continue, without claiming more than the observation supports |
| **Partially matched** | Narrow or revise the hypothesis |
| **Failed** | Abandon or replace it; revert unjustified speculative edits |
| **Inconclusive** | Treat it as no support and design a better discriminator |

## A small performance example

```text
Hypothesis: Repeated JSON parsing dominates request latency.
Expected observation: A profile attributes more than half of the slow path to JSON.parse.
Expected change: Caching the parsed value reduces p95 latency in the same benchmark.
Disproof: Parsing is a minor sample or p95 does not move outside run-to-run noise.
```

If the profile shows database wait time instead, the parsing optimization has lost its justification. It should not stay merely because it looks reasonable.

## Use it for

- Debugging where several causes could explain the same symptom.
- Performance work with measurable expected movement.
- Nontrivial fixes justified by a suspected causal mechanism.
- Speculative implementation changes that may need to be reverted.
- Investigations where a persuasive post-hoc story could hide a failed idea.

Explicit prompts:

```text
Use $bet-on-it before changing this cache invalidation logic.
Use $bet-on-it to make the next debugging step discriminate between two causes.
Use $bet-on-it and revert the edit if its prediction fails.
```

## Non-goals and limits

- It is not ceremony for formatting, renaming, or other mechanical edits.
- A matched prediction raises confidence; it does not prove a hypothesis uniquely true.
- It does not require a risky experiment. Safety and permission boundaries still control the action.
- An inconclusive result is allowed. The skill prevents it from being counted as confirmation.
- Predictions are short working records, not permanent design documents.

## The skill

The complete agent-facing protocol lives in [`SKILL.md`](./SKILL.md). The repository is intentionally instruction-only: no framework, service, or runtime dependency.

## Related skills

| Skill | Use it when |
| --- | --- |
| [`prove-me-wrong`](https://github.com/Lum1104/prove-me-wrong) | An established claim needs the cheapest useful counterexample |
| [`no-vibes`](https://github.com/Lum1104/no-vibes) | Completion must be tied to the real end-to-end outcome |
| [`archaeologist`](https://github.com/Lum1104/archaeologist) | Repository history may contain evidence about the suspected cause |
| [`red-button`](https://github.com/Lum1104/red-button) | The experiment or change has high-impact consequences |

Each repository is standalone. Combine them only when their failure modes overlap.

## License

[MIT](./LICENSE) © 2026 Lum1104
