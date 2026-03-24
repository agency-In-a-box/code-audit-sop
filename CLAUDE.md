# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

This is a documentation repository containing Standard Operating Procedures (SOPs) for conducting AI-assisted code audits. There is no application code, build system, or test suite — only versioned Markdown documents.

## Key Documents

- **`SOP - AI-Assisted Code Audit.md`** (v1.4.0) — The master audit framework: pre-audit checklists, pre-analysis shell commands, the master prompt template, parallel agent strategy, prompt variations (quick, security, performance, delta, migration, i18n, observability), post-audit deliverables, success criteria, risk matrix, finding ID conventions, and pricing tiers.
- **`SOP - Senior Developer Persona.md`** (v1.4.0) — The Senior Technical Architect system prompt that operationalizes the audit framework. Defines the persona, philosophy ("Measure Everything, Assume Nothing"), communication style, decision-making framework, weighted scoring model, and output templates.

Both documents reference a `skills/code-audit/` directory for a Claude skill version, but that directory does not yet exist in the repo.

## Architecture & Relationships

The two SOPs work together as a paired system:
1. The **Audit SOP** defines *what* to audit (14 categories), *how* to gather data (bash commands), and *what to deliver* (executive summary, technical report, remediation roadmap).
2. The **Persona SOP** defines *who* performs the audit — the behavioral instructions, expertise areas, and output formatting for the AI agent.

The audit uses a **weighted scoring model** for production readiness (Architecture 25%, Code Quality 20%, Testing/CI 20%, Security 15%, Performance 10%, Documentation 10%) and a **finding ID convention** (CSS-N, JS-N, A11Y-N, BUILD-N, ARCH-N, SEC-N, PERF-N, DOC-N, API-N, OBS-N, I18N-N, ASSET-N, LIC-N, GIT-N).

For large codebases, the SOP prescribes a **5-agent parallel strategy**: (1) Quantitative Metrics, (2) CSS Architecture, (3) JS/TS Quality, (4) Accessibility & Production Readiness, (5) Infrastructure & Resilience.

## Editing Guidelines

- Both documents share the same version number (currently 1.4.0) — keep them in sync when making changes.
- The Audit SOP's version history table at the bottom must be updated with any content changes.
- Finding ID prefixes are standardized — do not introduce new prefixes without adding them to the convention table in the Audit SOP.
- The 14 audit categories in both documents must stay aligned; adding a category to one requires updating the other.
