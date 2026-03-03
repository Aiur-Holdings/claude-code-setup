# AI Dev Setup

Battle-tested rules, templates, and workflow patterns for AI-assisted development. Agent-agnostic — works with Claude Code, Cursor, Codex, Windsurf, or any AI coding agent.

## What's Inside

```
ai-dev-setup/
├── README.md                  # You are here
├── INSTALL.md                 # Per-agent wiring instructions
├── PLAYBOOK.md                # Step-by-step: which workflow to run at each stage
├── REPO-STRUCTURE.md          # Recommended project layout for AI-assisted dev
├── global-preferences.md      # Personal working style (your global agent config)
├── rules/
│   ├── project.md             # Main project instructions template
│   ├── workflow.md            # Spec-driven dev, TDD, PR review flow
│   ├── code-patterns.md       # Idempotency, multi-tenancy, error handling
│   ├── testing.md             # TDD rules, test categories, depth checklist
│   ├── security.md            # Auth patterns, PII, logging, fail-closed
│   └── templates.md           # Template usage enforcement
├── workflows/                 # 16 structured workflow prompts
│   ├── README.md              # Catalog of all workflows
│   ├── spec.md                # Write specs with interview process
│   ├── tdd-workflow.md        # TDD RED phase (tests first)
│   ├── review.md              # Comprehensive code review
│   ├── pr.md                  # Automated review-fix loop
│   ├── grill.md               # Adversarial "break the code" review
│   ├── audit.md               # Multi-dimensional code audit
│   ├── security.md            # Security-focused checklist
│   ├── staff-review.md        # Senior engineer plan review
│   ├── sprint-closeout.md     # Sprint merge readiness gate
│   ├── commit.md              # Structured commits
│   ├── fix.md                 # Autonomous bug fixing
│   ├── backlog.md             # Agent-ready backlog items
│   ├── test-gen.md            # Test plan generation
│   ├── explain.md             # Code/PR/sprint explanations
│   ├── ingest-review.md       # Parse review findings
│   ├── check-tenancy.md       # Multi-tenancy validator
│   └── check-consistency.md   # String constant drift checker
├── templates/
│   ├── sprint-spec.md         # Sprint planning template (hard-capped sections)
│   └── work-order.md          # Agent delegation template (bounded scope)
└── wiring/
    ├── claude-code.md         # Setup for Claude Code (.claude/ structure)
    ├── codex.md               # Setup for OpenAI Codex (AGENTS.md)
    ├── cursor.md              # Setup for Cursor (.cursor/rules/)
    └── windsurf.md            # Setup for Windsurf (.windsurfrules)
```

## Philosophy

These rules encode a specific development philosophy:

- **Spec-driven**: Plan before code. Write specs, validate them, then implement.
- **TDD by default**: Tests first for all non-trivial changes.
- **Sacred tests**: Never modify existing tests to make code pass. If tests break, the code is wrong.
- **Ship complete**: Build it now or cut scope explicitly. Never defer to "future work."
- **Fail-closed**: Missing auth = error, not silent bypass. Least privilege always.
- **Proof required**: Claims about correctness must be backed by tests, logs, or reproducible checks.
- **Append-only audit**: Event and audit log tables are immutable. Never UPDATE or DELETE.

## Quick Start

1. **Read `PLAYBOOK.md`** — the end-to-end guide: which workflow to run, when, and why
2. Read `INSTALL.md` for your specific agent's setup
3. Copy `global-preferences.md` into your agent's global config location
4. Copy `rules/project.md` into your project and fill in the `<!-- CUSTOMIZE -->` sections
5. Copy additional `rules/` files as needed for your stack
6. Copy `workflows/` into your project — these are the actual prompts that drive the dev process
7. Copy `templates/` into your project for sprint planning and delegation
8. Read `REPO-STRUCTURE.md` for how to organize your project

## Customization

Files with `<!-- CUSTOMIZE -->` markers have sections you need to fill in for your specific project. Search for these markers and replace with your project's details.

Rules without customize markers are stack-agnostic and can be used as-is.

## Origin

Extracted from a production AI agent orchestration platform. Every rule exists because something went wrong without it.
