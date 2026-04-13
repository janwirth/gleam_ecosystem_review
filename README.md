# Gleam Ecosystem Reviews

Systematic evaluations of Gleam libraries. Cuts through noise: find which libraries actually work, which are maintained, which have good APIs.

## Problem

Choosing Gleam libraries is hard. Repos scatter across GitHub. Some unmaintained. Some have bugs or rough APIs. Some do something different than name suggests. Weeks spent digging.

## Solution

This repo collects systematic reviews. Each review:
- Tests actual behavior
- Checks maintenance status
- Evaluates API design
- Notes gotchas and tradeoffs
- Provides real judgment, not marketing

## Structure

```
reviews/
  library-name/
    review.md          # Full evaluation
    examples/          # Working code samples
    workflow.md        # Agentic review process (generated)
```

## Using Reviews

Read `/reviews/[library]/review.md` for complete evaluation. Skip the marketing. Find what actually matters for your use case.

## Contributing

Reviews use systematic workflow. See `workflow.md` files for how reviews are generated.
