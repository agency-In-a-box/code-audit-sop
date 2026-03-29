# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

This is a documentation repository containing Standard Operating Procedures (SOPs) for conducting AI-assisted audits. There is no application code, build system, or test suite — only versioned Markdown documents.

## Key Documents

- **`SOP - AI-Assisted Code Audit.md`** (v1.4.0) — The master code audit framework: pre-audit checklists, pre-analysis shell commands, the master prompt template, parallel agent strategy, prompt variations (quick, security, performance, delta, migration, i18n, observability), post-audit deliverables, success criteria, risk matrix, finding ID conventions, and pricing tiers.
- **`SOP - Senior Developer Persona.md`** (v1.4.0) — The Senior Technical Architect system prompt that operationalizes the code audit framework. Defines the persona, philosophy ("Measure Everything, Assume Nothing"), communication style, decision-making framework, weighted scoring model, and output templates.
- **`SOP - Design-Excellence-Audit.md`** (v1.2.0) — The design excellence audit for Amphibious/AIAB. Evaluates semantic HTML, typography (Avenir Next), Pantone 144 brand color, BEM/namespace conventions, spatial grid (4px baseline, 16-column), motion design, component craftsmanship, dark mode, design tokens, aesthetic distinctiveness (anti-AI-slop), theme system architecture, and visual regression integrity.

All three documents reference a `skills/code-audit/` directory for a Claude skill version, but that directory does not yet exist in the repo.

## Architecture & Relationships

### Code Audit System (paired documents)

The Audit SOP and Persona SOP work together:
1. The **Audit SOP** defines *what* to audit (14 categories), *how* to gather data (bash commands), and *what to deliver* (executive summary, technical report, remediation roadmap).
2. The **Persona SOP** defines *who* performs the audit — the behavioral instructions, expertise areas, and output formatting for the AI agent.

The code audit uses a **weighted scoring model** for production readiness (Architecture 25%, Code Quality 20%, Testing/CI 20%, Security 15%, Performance 10%, Documentation 10%) and a **5-agent parallel strategy**: (1) Quantitative Metrics, (2) CSS Architecture, (3) JS/TS Quality, (4) Accessibility & Production Readiness, (5) Infrastructure & Resilience.

### Design Excellence Audit (standalone)

The Design Excellence Audit is a standalone SOP with its own parallel strategy (5 agents: Design Tokens & Typography, Semantic HTML & Components, Visual Polish & Dark Mode, Aesthetic Distinctiveness & Theme Architecture, Visual Regression Capture). It has a separate scoring rubric (1-10 per category, 15 categories) and its own finding ID convention distinct from the code audit.

## Finding ID Conventions

**Code Audit prefixes** (defined in Audit SOP): CSS-N, JS-N, A11Y-N, BUILD-N, ARCH-N, SEC-N, PERF-N, DOC-N, API-N, OBS-N, I18N-N, ASSET-N, LIC-N, GIT-N

**Design Audit prefixes** (defined in Design Excellence SOP): TYPO-N, COLOR-N, SPACE-N, MOTION-N, COMP-N, HTML-N, DARK-N, A11Y-D-N, RESP-N, TOKEN-N, ASLOP-N, THEME-N, VREG-N

Do not introduce new prefixes without adding them to the convention table in the respective SOP.

## Editing Guidelines

- The Audit SOP and Persona SOP share the same version number (currently 1.4.0) — keep them in sync when making changes.
- The Design Excellence Audit has its own version (currently 1.2.0).
- Each SOP's version history table at the bottom must be updated with any content changes.
- The 14 code audit categories in the Audit SOP and Persona SOP must stay aligned; adding a category to one requires updating the other.
- The 15 design audit categories are self-contained in the Design Excellence SOP.
