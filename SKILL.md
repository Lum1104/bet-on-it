---
name: bet-on-it
description: Use when debugging or making a speculative behavior change where the current hypothesis should predict an observable result before the next experiment or edit.
---

# Bet On It

> Predict first. Explain later.

Make the next action earn or lose confidence in the current hypothesis. Never rewrite a prediction after seeing the result.

## Record the bet

Immediately before each meaningful experiment or change, write:

```text
Hypothesis: <current causal explanation>
Expected observation: <specific result expected from the next action>
Expected change: <files, state, metric, or behavior expected to change>
Disproof: <result that would make this hypothesis untenable>
```

Use concrete, observable signals. Replace “this should help” with a value, error, event, diff, or behavior that can be compared. Make one bet cover a tightly coupled change set; do not add ceremony to formatting, renaming, or other purely mechanical edits.

## Run the smallest discriminating step

Choose the cheapest safe experiment or change that separates the hypothesis from plausible alternatives. Preserve the pre-change baseline when comparison matters. Avoid bundling unrelated edits that make the result ambiguous.

## Settle the bet

After the action, preserve the original prediction and append:

```text
Observed: <actual result, including unexpected changes>
Verdict: matched | partially matched | failed | inconclusive
Next action: <continue, refine, abandon, revert, or gather a better signal>
```

Apply the verdicts consistently:

- **Matched**: The expected observation and change occurred, and the disproof condition did not.
- **Partially matched**: Some predictions held, but the mismatch requires a narrower or revised hypothesis.
- **Failed**: The disproof occurred or a required expected observation did not. Explicitly abandon or replace the hypothesis.
- **Inconclusive**: The step could not distinguish the hypothesis from alternatives. Do not count it as support; design a better discriminator.

Revert speculative changes after a failed hypothesis when they no longer have independent justification. If a change remains for another reason, state that new reason explicitly and test it under a new prediction.

## Example

Before changing a duplicated checkout request:

```text
Hypothesis: A React effect binds the submit handler twice after rerender.
Expected observation: Instrumentation records two client handler invocations per click.
Expected change: Correcting the effect dependency changes Checkout.tsx and reduces requests from two to one.
Disproof: Only one client invocation occurs while two server-side writes remain.
```

If instrumentation shows one invocation and two writes caused by a server retry, record **failed**, revert the speculative effect edit, and form a new retry/idempotency hypothesis. Do not retain the edit or describe the server result as confirmation of the original theory.
