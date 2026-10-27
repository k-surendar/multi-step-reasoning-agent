# Multi-Step Reasoning Agent with Self-Checking

## Overview
A Python notebook-based agent that solves supported arithmetic and time
word problems using three separate phases: Planner, Executor, and Verifier.

## Architecture
1. **Planner** extracts problem facts and creates a structured plan.
2. **Executor** computes a proposed answer and records key calculations.
3. **Verifier** independently recalculates the result and checks constraints.
4. **Retry loop** repeats execution when verification fails, up to two retries.
5. **JSON output** returns the answer, status, concise explanation, plan,
   verification checks, and retry count.

## Requirements
- Python 3
- Google Colab or a local Python environment
- Standard library only for the offline baseline

## How to run
1. Open `Multi_Step_Reasoning_Agent.ipynb` in Google Colab.
2. Run each cell from top to bottom.
3. Call `solve("your question here")`.
4. Run the evaluation suite to produce `example_run_logs.json`.

## Where prompts live
`PLANNER_PROMPT`, `EXECUTOR_PROMPT`, and `VERIFIER_PROMPT` are separate
templates near the start of the notebook. They document the intended LLM
interface and include examples for each phase.

The submitted baseline uses deterministic Python functions rather than
live LLM calls, so it can be tested without API credentials or quota.
The prompt templates are documentation for a future LLM backend and are
not executed by the offline implementation.

## Supported problem types
- Apple totals where green apples are twice the red apples
- Multi-step chocolate totals
- Item quantity multiplied by unit price
- Journey time differences, including journeys crossing midnight
- Selecting meeting slots long enough for a required duration

Unsupported question formats fail safely instead of guessing.

## Output schema
The `solve(question)` function returns a dictionary with:
- `answer`
- `status`
- `reasoning_visible_to_user`
- `metadata.plan`
- `metadata.checks`
- `metadata.retries`

The user-facing explanation is short. Detailed internal chain-of-thought
is not exposed.

## Evaluation
The notebook includes eight easy tests and four tricky tests, plus a
deliberate incorrect-answer test to demonstrate retry behavior.
Test logs are saved to `example_run_logs.json`.

## Prompt design
Each phase has a separate responsibility and a specified JSON structure.
Examples help define expected behavior and reduce output-format ambiguity.
The offline version uses deterministic rules so calculations and checks
are reproducible.

## Limitations and assumptions
- The baseline supports only the explicitly implemented question patterns.
- Time values use 24-hour `HH:MM` format.
- Overnight journeys are supported; meeting slots are assumed to start
  and end on the same day.
- The verifier shares the Planner's extracted facts, so incorrect parsing
  can affect both execution and verification.
- Prompt templates are not live model calls in this version.

## What did not work well
The initial Gemini-based prototype reached the free-tier daily API quota.
This prevented further live-model tests. The offline implementation was
built to continue development and validate the agent loop without API calls.

## Future improvements
- Add an actual LLM backend behind a configurable interface.
- Validate and retry malformed model JSON.
- Add broader arithmetic, logic, and constraint parsing.
- Use independent parsing or a second verifier to reduce shared errors.
- Add unit tests, CI, and more adversarial test cases.
