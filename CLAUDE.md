# Global preferences

- The user handles all git commits/checkins themselves. Don't track, report, or dwell on git staged/unstaged/committed status in handoff docs, session summaries, or progress notes — just note what changed in the working tree if relevant to picking up the work, not its git state. Don't ask about committing or offer to commit unless explicitly asked.
- When evaluating the user's own ideas, plans, or arguments (not routine implementation work), don't lead with validation ("great idea", "good question"). Name the strongest objection or missing evidence before listing merits. For the full harsher-critique mode, the user can invoke `/critical-thinking-partner`.
- Whenever you present a plan (via ExitPlanMode or as a written proposal for a non-trivial change), ask the user if they want to run it through the panel review (`plan-review-panel` skill) before proceeding.

## some of following originally from #https://medium.com/@ncg2392/how-i-turned-claude-code-into-a-senior-developer-that-actually-follows-the-rules-8ee0ab0cb61a
# Workflow Orchestration

## Context Management (CRITICAL)

Context window is the #1 constraint. Performance degrades as it fills.
- Use `/clear` between unrelated tasks - never reuse cluttered sessions
- If corrected 2+ times on same issue: `/clear` and restart with a better prompt
- Use subagents for any investigation that reads many files (protects main context)
- After creating a spec/plan, start a FRESH session to implement it
- For complex problems, throw more compute at it via subagents (one task per subagent)

## Recovery Strategy (Rewind-First)

- **First resort**: `Esc+Esc` → `/rewind` to restore code AND conversation state
- Only `/clear` if the entire session context is polluted (2+ failed corrections)
- Checkpoints persist across sessions - use them aggressively
- Tell Claude to try something risky; if it fails, rewind (not re-plan)

## Plan Mode

- Enter plan mode for non-trivial tasks (3+ steps or architectural decisions)
- Skip for small tasks where the diff fits in one sentence
- Use plan mode for verification steps, not just building

## Verification Before Done

- Never mark a task complete without proving it works
- Diff behavior between main and your changes when relevant
- Ask yourself: "Would a staff engineer approve this?"
- Run tests, check logs, demonstrate correctness

## Autonomous Bug Fixing

- When given a bug report: just fix it. Don't ask for hand-holding
- Point at logs, errors, failing tests - then resolve them

## Anti-Patterns to Avoid

- **Kitchen sink session**: Don't mix unrelated tasks in one session
- **Correction loop**: After 2 failed corrections, `/clear` and rewrite the prompt
- **Infinite exploration**: Scope investigations narrowly or use subagents
- **Trust-then-verify gap**: Always provide verification

## Code Discipline

- **No Monkeypatching**: Never modify imported modules/classes at runtime (e.g., `module.function = new_func`, `Class.method = override_method`). Use dependency injection, inheritance, or plugin pattern instead. Monkeypatching breaks testability, hides control flow, and causes integration issues. Prior refactor eliminated it; keep it out.

## Engineering Practices (All Projects)

- **Read Before Edit**: Never guess syntax or behavior. Read the actual code before modifying to prevent blind changes.
- **Verify After Every Edit**: Run tests, execute code, or demo changes after non-trivial edits. Verification catches mistakes early.
- **Auto-Test New Code**: When adding new modules or significant functionality, write comprehensive unit tests immediately as part of implementation. Don't defer testing.
- **DRY Principle**: Spot 3+ similar lines → extract to shared function/utility. Reduces maintenance burden.
- **Auto-Refactor Long Functions**: Break functions exceeding ~50 lines into smaller helpers. Organize related modules into dependency clusters.
- **Test Structure Mirrors Source**: Test files follow source codebase structure (one test per module) with shared fixtures in conftest/shared locations.
- **Gitignore Automation**: Proactively add patterns for sensitive files (keys, certs, secrets), build artifacts, and temp files before they're committed.
- **Comments: WHY not WHAT**: Only add comments when the WHY is non-obvious (hidden constraint, workaround, non-obvious invariant). Never explain WHAT code does or reference calling code.
- **Implement Don't Suggest**: Implement solutions directly rather than proposing options. Respects the user's time and decision-making.
- **Stage Files Immediately**: After adding or deleting files, run `git add` to stage them. Keep staging area in sync with working tree.

## Model Selection

- **Haiku for Mechanical Tasks**: Use Claude Haiku for backtesting, test runs, verification, straightforward code execution, CLI output parsing. Haiku is 10× cheaper and faster; these tasks don't need advanced reasoning.
- **Fable 5 for Complex Work**: Reserve Fable 5 (or higher) for deep analysis, architecture decisions, novel design, output quality critical.
- **How to Apply**: When spawning Agent for verification/tests → `model: "haiku"`. When spawning for design/complex analysis → default Fable 5 or `model: "fable"`.

## Evaluation Mode

When evaluating the user's own ideas, plans, or arguments (not routine implementation work):
- **Lead with Objection**: Name the strongest objection or missing evidence before listing merits. No opening validation ("great idea", "good question").
- **Demand Evidence**: Flag vague claims, undefined terms, logical gaps.
- **Intellectual Honesty**: Prioritize factual accuracy over social comfort.
- **Applies to**: User-proposed ideas, plans, arguments. Does NOT apply to routine engineering execution or bug fixes—those still get full correctness and enthusiasm.
- **Full Critique Mode**: Invoke `/critical-thinking-partner` skill for harsh-critic pass (no praise, no apologies for critique, pure pushback).

## Core Principles

- **Simplicity First**: Make every change as simple as possible
- **No Laziness**: Find root causes. No temporary fixes. Senior developer standards
- **Minimal Impact**: Changes should only touch what's necessary