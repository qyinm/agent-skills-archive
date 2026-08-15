---
name: ears-requirements
description: Write, refine, and review software or product requirements with EARS (Easy Approach to Requirements Syntax). Use when the user mentions EARS, SHALL requirements, acceptance criteria, requirements plans, or asks for deterministic and testable behavior descriptions.
disable-model-invocation: true
---

# EARS Requirements

Use EARS to turn ambiguous behavior descriptions into concise, reviewable, and testable requirements. Keep the requirement readable as natural language while making conditions, triggers, the responsible system, and the observable response explicit.

## Core rules

- Keep the EARS keywords in uppercase: `WHEN`, `WHILE`, `WHERE`, `IF`, `THEN`, and `SHALL`.
- Write in the user's language; when writing Korean requirements, keep the EARS keywords in English and write the condition and response naturally in Korean.
- Put clauses in temporal order: precondition/state → trigger → system → response.
- A requirement may have zero or more preconditions, at most one trigger, exactly one system name, and one or more system responses.
- Prefer one behavior and one observable outcome per requirement. Split unrelated outcomes into separate requirements.
- Name the system or component that is responsible for the behavior; do not use an undefined actor such as “it” or “the app.”
- Replace vague terms such as “quickly,” “normally,” “user-friendly,” “appropriate,” and “as needed” with measurable or observable conditions.
- Preserve domain terms supplied by the user. Do not invent thresholds, actors, failure policies, or business rules; mark missing information as an assumption or open question.

## Choose the pattern

Use the smallest pattern that expresses the behavior.

| Pattern | Form | Use when |
| --- | --- | --- |
| Ubiquitous | `THE <system> SHALL <response>.` | The behavior is always active. |
| Event-driven | `WHEN <trigger>, THE <system> SHALL <response>.` | A discrete event causes the response. |
| State-driven | `WHILE <precondition>, THE <system> SHALL <response>.` | The behavior applies while a state remains true. |
| Optional feature | `WHERE <feature is included>, THE <system> SHALL <response>.` | The behavior exists only for a product or configuration variant. |
| Unwanted behavior | `IF <undesired condition>, THEN THE <system> SHALL <response>.` | A fault, invalid input, error, or other unwanted situation occurs. |
| Complex | `WHILE <precondition>, WHEN <trigger>, THE <system> SHALL <response>.` | Both a state and a discrete trigger constrain the behavior. |

Do not force every requirement into EARS. Recommend a formula, decision table, state-transition diagram, interface contract, or separate non-functional requirement when that representation is clearer than a long conditional sentence.

## Workflow

1. Identify the goal, scope, user-visible or system-visible behavior, and affected system boundary.
2. Extract the condition, trigger, responsible system, response, timing, data constraints, and error behavior from the source material.
3. Select the smallest EARS pattern. Use `IF ... THEN` for unwanted behavior, not for ordinary events.
4. Write the requirement with one stable system name and an observable `SHALL` response.
5. Check that every condition and response is testable. Add a deterministic test oracle separately when the user is planning implementation or acceptance criteria.
6. Review for ambiguity, hidden assumptions, mixed behaviors, missing edge cases, and requirements that should use another representation.

## Output contract

When producing a requirements plan, use this structure unless the user asks for another format:

```markdown
# [Feature or change]

## Goal
[What outcome the change must achieve]

## Scope
- In scope: ...
- Out of scope: ...

## Assumptions and open questions
- Confirmed: ...
- Assumption: ...
- Open question: ...

## Requirements

### R-001 — [short title]
WHEN/WHILE/WHERE/IF ... THE [system] SHALL ...

Test oracle: Given ..., when ..., then ...

## Edge cases and failure behavior
- ...
```

Separate facts, user-stated judgments, agent inferences, and unverified assumptions. If a missing answer could change the requirement, ask one focused question or leave an explicit open question rather than silently choosing a value.

When reviewing an existing requirement, report findings using these categories:

- Pattern mismatch: the chosen keyword does not match the behavior.
- Missing condition or trigger: the requirement does not say when it applies.
- Ambiguous subject: the responsible system or actor is unclear.
- Untestable response: the `SHALL` clause cannot be observed or measured.
- Mixed behavior: multiple independent outcomes should be split.
- Hidden assumption: the sentence depends on an unstated policy, threshold, or data rule.
- Representation mismatch: a table, formula, state model, or separate quality attribute would be clearer.

## Examples

### Event-driven

```text
WHEN the user selects Save, THE Workspace SHALL persist the current document and return a success status.
```

Test oracle: Given a valid editable document, when Save is selected, then the document is retrievable with the saved content and the success status is returned.

### State-driven

```text
WHILE an export job is running, THE Export Service SHALL report its current progress percentage.
```

### Unwanted behavior

```text
IF the uploaded file exceeds the configured size limit, THEN THE Upload Service SHALL reject the file and return the size-limit error code.
```

### Complex

```text
WHILE the account is locked, WHEN a login attempt is submitted, THE Authentication Service SHALL deny the attempt and record the lockout reason.
```

## Quality gate

Before finalizing, verify:

- The requirement has exactly one clearly named system.
- The selected pattern matches the temporal meaning.
- `SHALL` describes an obligation, not a wish or implementation detail.
- Conditions, thresholds, outputs, and error handling are explicit enough to test.
- No undefined pronouns, vague modifiers, or unbounded lists remain.
- Each acceptance criterion has a deterministic oracle or a clearly stated reason why one cannot yet be written.
- Any out-of-scope or unresolved decision is visible to the reviewer.

Source: [Easy Approach to Requirements Syntax](https://en.wikipedia.org/wiki/Easy_Approach_to_Requirements_Syntax)
