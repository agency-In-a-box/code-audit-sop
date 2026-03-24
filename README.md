# AI-Assisted Code Audit SOP

A standardized framework for conducting thorough codebase audits using AI tools (Claude, ChatGPT, etc.) to evaluate production readiness, technical debt, and architectural issues.

## Getting Started

For best results, follow this two-step workflow:

### Step 1: Load the Senior Developer Persona

Start by giving your AI assistant the contents of **`SOP - Senior Developer Persona.md`**. This establishes the Senior Technical Architect persona — a security-focused, metrics-driven auditor that produces quantitative, structured reports with finding IDs, effort estimates, and weighted scoring.

Paste the full contents of the persona file as a system prompt or as the first message in your conversation.

### Step 2: Run the Code Audit

With the persona active, follow the process in **`SOP - AI-Assisted Code Audit.md`**:

1. Complete the **Pre-Audit Checklist** — gather source code, dependency manifests, build configs, and any previous audit reports.
2. Run the **Pre-Analysis Commands** against the target codebase to collect quantitative data (LOC, dependency counts, security scans, etc.).
3. Feed the results along with the **Master Prompt** (customized for the target codebase) to produce a full audit across 14 categories.
4. For large codebases, use the **Parallel Agent Strategy** to split work across 5 specialized agents.

### Prompt Variations

The audit SOP includes targeted prompt variations for specific needs:

| Variation | Use When |
|-----------|----------|
| Quick Assessment | You need a 15-minute, 300-word verdict |
| Security-Focused | Security is the primary concern |
| Performance Audit | Optimizing load times, bundle size, memory |
| Delta Audit | Re-auditing a previously reviewed codebase |
| Migration Assessment | Evaluating a move to a new tech stack |
| i18n Readiness | Preparing for internationalization |
| Observability Assessment | Evaluating production debugging capability |

## What You Get

Each full audit produces three deliverables:

1. **Executive Summary** — Overall score (X/10), GO/NO-GO recommendation, top 3 blocking issues
2. **Technical Report** — Detailed findings across 14 categories, each tagged with finding IDs (e.g., CSS-1, SEC-2, API-3)
3. **Remediation Roadmap** — Phased plan (Quick Wins / Phase 1 / Phase 2 / Phase 3) with effort and cost estimates

## Audit Categories

The framework covers 14 audit categories:

1. CSS/Styling Architecture
2. JavaScript/TypeScript Quality
3. Accessibility (A11y)
4. Build System & DevOps
5. Architecture & Design Patterns
6. Quantitative Code Health Metrics
7. Production Readiness Assessment
8. TypeScript/Compilation Health
9. Development Environment Assessment
10. Security Posture
11. API & Network Resilience
12. Observability & Logging
13. Internationalization (i18n)
14. Asset Optimization

## Multi-Language Support

Pre-analysis commands and framework-specific analysis cover JavaScript/TypeScript, Python, Go, Rust, and GraphQL.

## License

See repository for license details.
