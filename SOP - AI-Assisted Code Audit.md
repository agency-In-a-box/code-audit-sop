# SOP: AI-Assisted Code Audit

**Version:** 1.4.0
**Created:** December 2, 2025
**Updated:** March 23, 2026

> **Related Documents:**
> - **Persona:** See "SOP - Senior Developer Persona.md" for the Senior Technical Architect system prompt that operationalizes this audit framework.
> - **Skill:** See "skills/code-audit/" for the Claude skill version of this SOP that can be invoked directly.
**Purpose:** Standardized process for conducting thorough codebase audits using AI assistance

---

## Overview

This SOP enables consistent, comprehensive code audits using AI tools (Claude, ChatGPT, etc.) to evaluate codebases for production readiness, technical debt, and architectural issues.

---

## Pre-Audit Checklist

Before running the audit, gather:

- [ ] Full source code (zip or repo access)
- [ ] Package.json / requirements.txt / Cargo.toml / go.mod (dependency manifest)
- [ ] Build configuration files (vite.config, webpack.config, tsconfig, Dockerfile, docker-compose, etc.)
- [ ] Any existing documentation or README
- [ ] Previous audit reports (for delta comparison)
- [ ] Client's specific concerns or focus areas (if any)
- [ ] .env.example or environment variable documentation (if any)
- [ ] Infrastructure/deployment configuration (Terraform, Kubernetes manifests, CI configs)

---

## Pre-Analysis Commands

Run these commands to gather quantitative data before the AI audit:

```bash
# === GENERAL CODE METRICS ===
# Lines of code by language (JS/TS/Svelte/Python/Go/Rust)
find . -name "*.js" -o -name "*.ts" -o -name "*.svelte" -o -name "*.py" -o -name "*.go" -o -name "*.rs" | grep -v node_modules | grep -v __pycache__ | grep -v target/ | xargs wc -l
grep -r "TODO\|FIXME\|HACK\|XXX\|DEPRECATED" src/ | wc -l
grep -r "console\." src/ | wc -l
grep -r "!important" src/ --include="*.css" --include="*.scss" | wc -l

# TypeScript compilation check
npm run check || npx tsc --noEmit 2>&1 | grep -c "error TS"

# === SECURITY CHECKS ===
grep -r "password\|secret\|key\|token" src/ --exclude-dir=node_modules
npm audit || yarn audit || pnpm audit
# Git history secret scan (requires git-secrets or trufflehog)
git log --all --diff-filter=A --name-only --pretty=format: | grep -iE "\.env$|\.pem$|\.key$|id_rsa" | head -20

# === DEPENDENCY ANALYSIS ===
npm list --depth=0 | wc -l
npm outdated | wc -l

# License compliance (NEW in 1.4.0)
npx license-checker --summary 2>/dev/null || echo "Install: npm install -g license-checker"
npx license-checker --failOn "GPL-2.0;GPL-3.0;AGPL-3.0" 2>/dev/null || echo "Check for copyleft licenses manually"

# === DEVELOPMENT ENVIRONMENT ===
ps aux | grep -E "(npm|node|vite)" | grep -v grep | wc -l
lsof -ti:5173,3000,8080,4000 | wc -l

# Test coverage (if configured)
npm test -- --coverage 2>/dev/null || echo "No tests configured"

# === MEMORY SAFETY ===
echo "addEventListener calls:"
grep -r "addEventListener" src/ --include="*.ts" --include="*.js" | wc -l
echo "removeEventListener calls:"
grep -r "removeEventListener" src/ --include="*.ts" --include="*.js" | wc -l

# === CSS/THEMING ===
# Dark mode / theme coverage
grep -r 'data-theme\|prefers-color-scheme' src/ --include="*.css" | wc -l

# Accessibility media queries
grep -r "prefers-reduced-motion" src/ --include="*.css" | wc -l
grep -r "prefers-contrast" src/ --include="*.css" | wc -l
grep -r "@media print" src/ --include="*.css" | wc -l

# Design token adoption
echo "CSS custom property definitions:"
grep -r "^  --" src/ --include="*.css" | wc -l
echo "CSS custom property usages:"
grep -r "var(--" src/ --include="*.css" | wc -l

# === CODE QUALITY ===
# Code duplication indicators
echo "Shared utility patterns (window.__*):"
grep -r "window\.__" src/ --include="*.js" --include="*.ts" | wc -l

# Dead code indicators (NEW in 1.4.0)
echo "Unused exports (requires ts-unused-exports or knip):"
npx knip --no-exit-code 2>/dev/null | head -30 || echo "Install: npm install -g knip"

# Code complexity (NEW in 1.4.0)
npx cr src/ 2>/dev/null | head -20 || echo "Install: npm install -g complexity-report"

# === i18n CHECKS (NEW in 1.4.0) ===
echo "Hardcoded user-facing strings (rough estimate):"
grep -rn ">[A-Z][a-z]" src/ --include="*.jsx" --include="*.tsx" --include="*.svelte" --include="*.html" | wc -l
echo "i18n function usage:"
grep -r "t(\|i18n\.\|useTranslation\|$t(" src/ --include="*.ts" --include="*.js" --include="*.svelte" --include="*.jsx" --include="*.tsx" | wc -l

# === OBSERVABILITY (NEW in 1.4.0) ===
echo "Structured logging usage:"
grep -r "logger\.\|winston\.\|pino\.\|bunyan\.\|log\.info\|log\.error\|log\.warn" src/ --include="*.ts" --include="*.js" | wc -l
echo "Error tracking integration:"
grep -r "Sentry\.\|Bugsnag\.\|Rollbar\.\|TrackJS\|LogRocket" src/ --include="*.ts" --include="*.js" | wc -l

# === DOCKER/CONTAINER (NEW in 1.4.0) ===
echo "Dockerfile present:"
find . -name "Dockerfile*" -o -name "docker-compose*" | head -10
echo "Dockerfile stages:"
grep -c "^FROM" Dockerfile 2>/dev/null || echo "No Dockerfile found"
echo "Docker image size (if built):"
docker images --format "{{.Repository}}:{{.Tag}} {{.Size}}" 2>/dev/null | head -5

# === GIT HYGIENE (NEW in 1.4.0) ===
echo "Large files in git history (>1MB):"
git rev-list --objects --all | git cat-file --batch-check='%(objecttype) %(objectname) %(objectsize) %(rest)' | awk '$1 == "blob" && $3 > 1048576 {print $3, $4}' | sort -rn | head -10
echo ".gitignore present:" && test -f .gitignore && echo "yes" || echo "NO"
echo "Sensitive files tracked:"
git ls-files | grep -iE "\.env$|\.pem$|\.key$|credentials|secret" | head -10

# === ENVIRONMENT VARIABLE MANAGEMENT (NEW in 1.4.0) ===
echo ".env.example exists:" && test -f .env.example && echo "yes" || echo "NO"
echo "Runtime env validation:"
grep -r "process\.env\." src/ --include="*.ts" --include="*.js" | wc -l
grep -r "z\.string\|joi\.\|yup\.\|env\.require\|envalid" src/ --include="*.ts" --include="*.js" | wc -l

# === ASSET OPTIMIZATION (NEW in 1.4.0) ===
echo "Unoptimized image formats:"
find src/ public/ -name "*.png" -o -name "*.jpg" -o -name "*.jpeg" -o -name "*.gif" 2>/dev/null | wc -l
echo "Modern image formats (WebP/AVIF):"
find src/ public/ -name "*.webp" -o -name "*.avif" 2>/dev/null | wc -l
echo "Images missing lazy loading:"
grep -rn "<img" src/ --include="*.html" --include="*.jsx" --include="*.tsx" --include="*.svelte" | grep -v "loading=" | wc -l

# === API/NETWORK RESILIENCE (NEW in 1.4.0) ===
echo "Fetch/axios calls without timeout:"
grep -rn "fetch(\|axios\." src/ --include="*.ts" --include="*.js" | wc -l
echo "Retry logic present:"
grep -r "retry\|retries\|backoff\|exponential" src/ --include="*.ts" --include="*.js" | wc -l

# === FRAMEWORK DETECTION ===
find . -name "*.svelte" | wc -l
find . -name "*.tsx" -o -name "*.jsx" | wc -l
find . -name "*.vue" | wc -l

# === PYTHON-SPECIFIC (NEW in 1.4.0) ===
echo "Python type hints coverage:"
grep -r "def " src/ --include="*.py" | wc -l
grep -r "def .*->.*:" src/ --include="*.py" | wc -l
echo "Python linting config:"
find . -name "pyproject.toml" -o -name ".flake8" -o -name ".pylintrc" -o -name "ruff.toml" | head -5

# === GO-SPECIFIC (NEW in 1.4.0) ===
echo "Go error handling (unchecked errors):"
grep -rn "_, err :=" src/ --include="*.go" | wc -l
grep -rn "if err != nil" src/ --include="*.go" | wc -l
```

---

## The Master Prompt

Use this prompt template, customizing the bracketed sections:

```
You are conducting a comprehensive technical audit of [CODEBASE NAME].

Review the attached source files and provide a detailed technical report covering:

1. **CSS/Styling Architecture**
   - Count of !important declarations
   - Duplicate or conflicting component definitions
   - Token/variable collisions
   - Dead or legacy CSS (unused libraries, outdated resets)
   - Missing modern CSS features (Grid, @layer, container queries, nesting)
   - Namespace isolation compliance (if applicable)
   - Dark mode / theme coverage percentage
   - Design token adoption rate (hardcoded values vs CSS custom properties)
   - Media query consistency (breakpoint tokens vs hardcoded pixels)

2. **JavaScript/TypeScript Quality**
   - Memory leak patterns (event listeners, closures, DOM references)
   - addEventListener vs removeEventListener ratio and cleanup patterns
   - Error handling coverage (try/catch per module)
   - Type safety issues (if TS)
   - Bundle size concerns
   - Deprecated API usage
   - Code duplication (shared utilities repeated across modules)
   - XSS/injection risks (innerHTML without sanitization)
   - Dead code and unused exports
   - Cyclomatic/cognitive complexity hotspots

3. **Accessibility (A11y)**
   - ARIA implementation (focus traps, live regions, labelledby)
   - Color contrast compliance (WCAG AA minimum)
   - Keyboard navigation support (per component)
   - Screen reader compatibility (aria-live for dynamic content)
   - Motion/animation considerations (prefers-reduced-motion)
   - High contrast mode support (prefers-contrast)
   - Print stylesheet coverage
   - Focus indicator visibility and contrast

4. **Build System & DevOps**
   - CI/CD pipeline configuration and completeness
   - Container/deployment strategy assessment
   - Environment management and configuration
   - Dependency management and vulnerability scanning
   - Test automation infrastructure
   - Code quality gates and linting setup
   - Accessibility scanning in CI (axe-core, pa11y, etc.)
   - Visual regression testing (if applicable)
   - Docker image optimization (multi-stage builds, image size, .dockerignore)

5. **Architecture & Design Patterns**
   - Code organization and modularity assessment
   - Design pattern usage and consistency
   - API design quality (RESTful principles, GraphQL schema)
   - Database schema design and optimization opportunities
   - Caching strategy implementation
   - Error handling patterns and logging consistency
   - Component lifecycle patterns (init/destroy, cleanup)
   - Module initialization consistency and race conditions

6. **Quantitative Code Health Metrics**
   - Lines of code by language
   - Technical debt ratio (TODO/FIXME count vs LOC)
   - Test coverage percentages (if available)
   - Dependency count and age analysis
   - Bundle size measurements (raw and gzipped)
   - addEventListener/removeEventListener ratio
   - Design token adoption rate
   - Cyclomatic complexity distribution (files above threshold)
   - Dead code / unused export count

7. **Production Readiness Assessment**
   - Rate overall production readiness: X/10 using weighted scoring:
     * Architecture Quality: 25%
     * Code Quality & Maintainability: 20%
     * Testing & CI/CD: 20%
     * Security & Compliance: 15%
     * Performance & Scalability: 10%
     * Documentation & Developer Experience: 10%
   - Provide clear GO/NO-GO/CONDITIONAL GO recommendation
   - List blocking issues that must be resolved before production
   - List recommended improvements with effort estimates
   - Provide remediation timeline with cost analysis
   - If previous audit exists, include delta comparison table

8. **TypeScript/Compilation Health** (if applicable)
   - Count of TypeScript compilation errors
   - Missing type declarations and any issues
   - Interface/type consistency across modules
   - Build process reliability and configuration quality
   - `any` type usage count
   - Non-null assertion (`!`) usage count

9. **Development Environment Assessment**
   - Development server stability and configuration
   - Hot module replacement functionality
   - Build tool configuration and optimization
   - Port conflicts and process management

10. **Security Posture**
    - XSS prevention (sanitization library usage, innerHTML audits)
    - Dependency vulnerability scan results
    - Secret/credential exposure scan
    - Content Security Policy readiness
    - npm package configuration security (files field, registry config)
    - License compliance (copyleft license contamination risk)
    - Git history hygiene (secrets in commits, large binary files)

11. **API & Network Resilience** (NEW in 1.4.0)
    - Timeout configuration on all HTTP calls (fetch, axios, etc.)
    - Retry logic with exponential backoff for transient failures
    - Circuit breaker patterns for external service dependencies
    - Rate limiting implementation (client-side and server-side)
    - API error response consistency and schema validation
    - Graceful degradation when dependencies are unavailable
    - Request cancellation on component unmount (AbortController usage)

12. **Observability & Logging** (NEW in 1.4.0)
    - Structured logging implementation (not raw console.log)
    - Error tracking integration (Sentry, Bugsnag, etc.)
    - Distributed tracing support (OpenTelemetry, etc.)
    - Health check endpoints
    - Alerting and monitoring configuration
    - Log levels used appropriately (debug/info/warn/error)
    - Sensitive data scrubbing from logs

13. **Internationalization (i18n)** (NEW in 1.4.0)
    - Hardcoded user-facing strings vs i18n function usage
    - Translation file structure and completeness
    - RTL layout support (if applicable)
    - Date/time/number/currency locale-aware formatting
    - Pluralization handling
    - Dynamic content translation coverage

14. **Asset Optimization** (NEW in 1.4.0)
    - Image format audit (PNG/JPG vs WebP/AVIF)
    - Lazy loading implementation for images and heavy components
    - Font loading strategy (font-display, subsetting, preload)
    - SVG optimization and inline vs file usage
    - Static asset caching headers and fingerprinting

**Output Format:**
- Use specific file names and line counts where possible
- Quantify issues (e.g., "319 !important declarations across 45 files")
- Assign finding IDs (e.g., CSS-1, JS-2, A11Y-3) for tracking
- Provide a clear GO/NO-GO/CONDITIONAL GO recommendation
- Include risk assessment matrix (Critical/High/Medium/Low)
- Include effort estimates for each finding (hours/days)
- Suggest alternative stacks if current approach is fundamentally flawed

**Technology Stack Assessment:**
- Evaluate framework/library appropriateness for requirements
- Assess version currency and LTS support status
- Consider ecosystem maturity and community support
- Estimate migration complexity if stack changes needed
- License risk assessment for all production dependencies

[OPTIONAL: Add specific focus areas]
Focus particularly on: [e.g., "performance for mobile devices", "enterprise security requirements", "accessibility compliance for government contracts", "internationalization for European markets"]
```

---

## Parallel Agent Strategy

For large codebases, split the audit across parallel agents for speed:

**Agent 1 — Quantitative Metrics**: Run all pre-analysis commands, compile LOC, dependency counts, bundle sizes, test results, build/lint/typecheck status, complexity scores, dead code report.

**Agent 2 — CSS Architecture**: Analyze CSS files for !important, namespace, dark mode coverage, token adoption, specificity, media queries, cascade order, dead code.

**Agent 3 — JS/TS Quality**: Analyze JS/TS files for memory leaks, error handling, type safety, API consistency, deprecated APIs, code duplication, XSS risks, test coverage gaps, complexity hotspots.

**Agent 4 — Accessibility & Production Readiness**: Analyze ARIA implementation, focus management, keyboard navigation, color contrast, CI/CD pipeline, package configuration, documentation completeness, version management.

**Agent 5 — Infrastructure & Resilience** (NEW in 1.4.0): Analyze Docker configuration, API/network patterns, observability setup, environment variable management, git hygiene, license compliance, logging architecture.

Compile all agent results into the final report with weighted scoring.

---

## Prompt Variations

### Quick Assessment (15-minute review)
```
Provide a rapid technical assessment of this codebase. Focus only on:
1. Top 3 critical issues that would block production deployment
2. Overall architecture quality (1-10)
3. Estimated technical debt in developer-days
4. One-line recommendation: ship, fix then ship, or rebuild

Be concise. No more than 300 words.
```

### Security-Focused Audit
```
Conduct a security-focused code review. Identify:
1. Input validation gaps
2. Authentication/authorization weaknesses
3. Exposed secrets or credentials (including git history)
4. SQL injection / XSS vulnerabilities
5. Dependency vulnerabilities (outdated packages with CVEs)
6. OWASP Top 10 compliance gaps
7. innerHTML usage without sanitization
8. Content Security Policy compliance
9. License compliance risk (copyleft contamination)
10. Sensitive data in logs or error messages

Rate security posture: X/10
List critical vulnerabilities requiring immediate attention.
Include specific remediation steps for each finding.
```

### Performance Audit
```
Analyze this codebase for performance issues:
1. Bundle size analysis (identify bloat, raw vs gzipped)
2. Render-blocking resources
3. Memory leak patterns (addEventListener/removeEventListener ratio)
4. Inefficient algorithms or data structures
5. Database query optimization opportunities
6. Caching strategy gaps
7. Lazy loading opportunities
8. Tree-shaking configuration (sideEffects field)
9. Image/asset optimization (formats, compression, lazy loading)
10. Font loading strategy (font-display, subsetting)
11. API call efficiency (batching, deduplication, cancellation)

Provide specific metrics where possible (KB saved, estimated load time impact).
Include performance budgets and optimization recommendations.
```

### Delta Audit (Repeat Audit)
```
This is a follow-up audit. The previous audit scored [X/10] on [DATE].

Compare current state to previous findings:
1. Which previous findings have been resolved?
2. Which previous findings remain open?
3. What new issues have been introduced?
4. How has the score changed per category?
5. Is the trend positive, negative, or flat?

Provide a delta comparison table and updated overall score.
```

### Migration Assessment
```
Evaluate this codebase for migration to [TARGET STACK].

Assess:
1. Code portability (what transfers cleanly vs. requires rewrite)
2. Estimated migration effort in developer-weeks
3. Risk factors during migration
4. Recommended migration sequence
5. Components that should be rebuilt vs. migrated

Provide a phased migration roadmap with cost/benefit analysis.
```

### i18n Readiness Assessment (NEW in 1.4.0)
```
Evaluate this codebase for internationalization readiness:
1. Count of hardcoded user-facing strings vs i18n-wrapped strings
2. Translation infrastructure (i18next, vue-i18n, svelte-i18n, etc.)
3. Locale-aware formatting (dates, numbers, currency, plurals)
4. RTL layout support readiness
5. Dynamic content translation gaps
6. Character encoding handling (UTF-8 consistency)
7. Third-party content/library locale support

Rate i18n readiness: X/10
Provide effort estimate to reach full internationalization.
```

### Observability Assessment (NEW in 1.4.0)
```
Evaluate this codebase for production observability:
1. Logging infrastructure (structured logging vs console.*)
2. Error tracking service integration
3. Distributed tracing implementation
4. Health check and readiness probe endpoints
5. Metrics collection (custom business metrics, performance metrics)
6. Alerting configuration
7. Log aggregation and search capability
8. Sensitive data scrubbing in logs and error reports
9. Correlation IDs for request tracing

Rate observability readiness: X/10
List critical gaps that would make production debugging blind.
```

### Framework-Specific Analysis
```
**If React detected, also analyze:**
- Component architecture and reusability
- State management patterns (Redux, Context, etc.)
- Performance optimization (memo, useMemo, lazy loading)
- Hydration mismatches (if SSR/Next.js)

**If Node.js/Express detected, also analyze:**
- Route organization and middleware patterns
- Database connection and query optimization
- Session management and authentication flows
- Graceful shutdown handling

**If SvelteKit detected, also analyze:**
- Page and layout architecture
- Store management patterns (Svelte stores)
- SSR/CSR configuration and performance
- Route parameter handling and type safety
- Component composition and reusability
- Hydration error patterns

**If CSS Framework detected, also analyze:**
- Namespace isolation strategy
- Design token architecture
- Dark mode / theme system coverage
- @custom-media or breakpoint token consistency
- Component-level vs global dark mode patterns

**If Database detected, also analyze:**
- Schema design and normalization
- Query optimization opportunities
- Migration strategy assessment
- Connection pool configuration

**If GraphQL detected, also analyze:** (NEW in 1.4.0)
- Schema complexity and depth limiting
- N+1 query detection (dataloader usage)
- Query cost analysis / rate limiting
- Subscription cleanup patterns
- Schema documentation completeness

**If Python detected, also analyze:** (NEW in 1.4.0)
- Type hint coverage (function signatures, return types)
- Virtual environment and dependency pinning
- Async/await patterns and event loop handling
- WSGI/ASGI server configuration

**If Go detected, also analyze:** (NEW in 1.4.0)
- Error handling patterns (unchecked errors)
- Goroutine leak potential
- Context propagation consistency
- Module dependency hygiene
```

---

## Post-Audit Deliverables

After running the audit, compile:

1. **Executive Summary** (1 page)
   - Overall readiness score with weighted breakdown
   - GO/NO-GO/CONDITIONAL GO recommendation with clear criteria
   - Top 3 blocking issues with effort estimates
   - Technology stack assessment summary
   - Delta comparison (if previous audit exists)

2. **Technical Report** (Full analysis)
   - Quantitative metrics dashboard
   - Detailed findings by category with file references and finding IDs
   - Risk assessment matrix with effort estimates
   - Security vulnerability assessment
   - Accessibility compliance matrix (WCAG criteria)
   - License compliance summary
   - Observability maturity assessment

3. **Remediation Roadmap** (Actionable)
   - Phased implementation plan (Quick Wins / Phase 1 / Phase 2 / Phase 3)
   - Effort estimates in developer-days/weeks
   - Cost estimates by phase
   - Risk mitigation strategies
   - Success criteria for each phase

---

## Success Criteria Templates

### Testing Infrastructure
- Minimum 70% code coverage
- All critical paths covered by integration tests
- CI pipeline runs tests on every PR
- Module coverage: all core modules have dedicated test files

### Security
- Zero high/critical vulnerability findings
- All user inputs validated and sanitized
- Authentication/authorization properly implemented
- No innerHTML usage without sanitization library
- npm audit clean
- No copyleft license contamination in production dependencies
- No secrets in git history
- Sensitive data scrubbed from logs

### Performance
- Initial page load under 3 seconds
- Bundle size under 200KB gzipped (CSS + JS)
- Database queries optimized (no N+1 problems)
- Development server starts without port conflicts
- Production dependencies minimized (< 5 for libraries)
- Images served in modern formats (WebP/AVIF) with lazy loading
- All HTTP calls have timeout configuration

### Accessibility
- WCAG AA compliant color contrast (4.5:1 for text, 3:1 for large text)
- All interactive components keyboard-navigable
- Focus indicators visible with sufficient contrast
- prefers-reduced-motion respected for all animations
- aria-live regions for dynamic content changes

### Observability (NEW in 1.4.0)
- Structured logging (not console.log) in all server-side code
- Error tracking service integrated and capturing unhandled exceptions
- Health check endpoint returning service status
- Request correlation IDs propagated across services
- No sensitive data (PII, credentials) in logs or error reports

### DevOps
- Automated deployment pipeline
- Environment configuration management
- Monitoring and alerting configured
- Build process completes without compilation errors
- Linter runs with zero errors
- Docker images use multi-stage builds and are under 500MB
- .env.example documents all required environment variables

### API Resilience (NEW in 1.4.0)
- All external HTTP calls have timeouts configured
- Retry logic with backoff for transient failures
- Graceful degradation when dependencies are unavailable
- Request cancellation on component unmount (AbortController)
- Consistent error response format across all endpoints

---

## Risk Assessment Matrix

**CRITICAL:** Security vulnerabilities (XSS, injection), compilation errors, production blockers, data loss risks, copyleft license contamination, secrets in git history
**HIGH:** Performance issues, scalability concerns, major technical debt, unmanaged dependencies, memory leaks, no error tracking in production, missing timeouts on external calls
**MEDIUM:** Maintainability issues, missing best practices, incomplete dark mode, code duplication, excessive console statements, no structured logging, missing i18n infrastructure, unoptimized assets
**LOW:** Code style inconsistencies, minor optimizations, documentation gaps, missing package.json fields, incomplete .env.example

---

## Finding ID Convention

Assign IDs to all findings for tracking across audits:

| Prefix | Category |
|--------|----------|
| CSS-N | CSS/Styling Architecture |
| JS-N | JavaScript/TypeScript Quality |
| A11Y-N | Accessibility |
| BUILD-N | Build System & DevOps |
| ARCH-N | Architecture & Design Patterns |
| SEC-N | Security |
| PERF-N | Performance |
| DOC-N | Documentation |
| API-N | API & Network Resilience |
| OBS-N | Observability & Logging |
| I18N-N | Internationalization |
| ASSET-N | Asset Optimization |
| LIC-N | License Compliance |
| GIT-N | Git Hygiene |

Example: `CSS-1: Dark mode coverage at 65% (22 components missing)` — tracked across audits.

---

## Pricing Tiers

| Tier | Scope | Deliverables | Price |
|------|-------|--------------|-------|
| Quick Scan | Single repo, <10K LOC | Executive summary only | $150 |
| Standard | Single repo, any size | Full report + roadmap | $400 |
| Enterprise | Multi-repo, full stack | Full report + roadmap + presentation | $800+ |
| Delta Audit | Repeat audit with comparison | Delta report + updated roadmap | $250 |

---

## Tips for Better Results

1. **Upload actual source files** — AI can grep and count; descriptions yield generic advice
2. **Include build configs** — These reveal architectural decisions
3. **Ask for specifics** — "Count the !important declarations" beats "check CSS quality"
4. **Request a verdict** — "Score it 1-10" forces the AI to commit rather than hedge
5. **Run static analysis first** — Feed the AI grep/lint output for even more precision
6. **Provide context** — Include information about user base, traffic expectations, compliance requirements
7. **Be framework-specific** — Mention if this is React, Vue, Node.js, CSS framework, etc. for targeted analysis
8. **Use parallel agents** — Split audit across 3-5 agents for large codebases (CSS, JS, A11y, Metrics, Infrastructure)
9. **Include previous audit** — If this is a repeat audit, include the prior report for delta comparison
10. **Check addEventListener balance** — The add/remove ratio reveals memory leak discipline at a glance
11. **Run license-checker early** — A copyleft dependency in production can invalidate the entire delivery
12. **Check git history** — Secrets removed from code may still live in commit history
13. **Verify timeouts exist** — Every fetch/axios call without a timeout is a production hang waiting to happen

### Enhanced Command Templates
```bash
# Count !important declarations
grep -r "!important" src/ --include="*.css" --include="*.scss" | wc -l

# Find console statements left in code
grep -rn "console.log\|console.error\|console.warn" src/ --include="*.ts" --include="*.js"

# Check for accessibility attributes
grep -r "alt=\|aria-\|role=\|tabindex" src/ --include="*.html" --include="*.jsx" --include="*.tsx" | wc -l

# Analyze event listeners (potential memory leaks)
echo "addEventListener:" && grep -r "addEventListener" src/ --include="*.ts" --include="*.js" | wc -l
echo "removeEventListener:" && grep -r "removeEventListener" src/ --include="*.ts" --include="*.js" | wc -l

# Check error handling patterns
grep -r "try" src/ --include="*.ts" --include="*.js" | wc -l
grep -r "catch" src/ --include="*.ts" --include="*.js" | wc -l

# Find TODO/FIXME items
grep -rn "TODO\|FIXME\|HACK\|XXX" src/ --exclude-dir=node_modules

# Dependency analysis
npm list --depth=0 | wc -l
npm outdated
npm audit --audit-level moderate

# Bundle size analysis (if built)
du -sh dist/ build/ public/js/ 2>/dev/null | head -5

# Design token adoption
echo "Token definitions:" && grep -r "^  --" src/ --include="*.css" | wc -l
echo "Token usages:" && grep -r "var(--" src/ --include="*.css" | wc -l

# Dark mode coverage
grep -r "prefers-color-scheme\|data-theme" src/ --include="*.css" | wc -l

# Accessibility media queries
grep -r "prefers-reduced-motion\|prefers-contrast\|@media print" src/ --include="*.css" | wc -l

# XSS risk scan (innerHTML without sanitization)
grep -rn "innerHTML" src/ --include="*.ts" --include="*.js"

# Code duplication indicators
grep -rn "window\.__" src/ --include="*.ts" --include="*.js"

# Namespace compliance (if .aiab- or similar prefix used)
# grep -r "class=\"[^\"]*\b(?!aiab-)" src/ --include="*.html" -P | head -20

# License audit (NEW in 1.4.0)
npx license-checker --csv --out licenses.csv
npx license-checker --failOn "GPL-2.0;GPL-3.0;AGPL-3.0;SSPL-1.0"

# Dead code detection (NEW in 1.4.0)
npx knip --reporter compact

# Git hygiene (NEW in 1.4.0)
git log --all --oneline | wc -l
git rev-list --objects --all | git cat-file --batch-check='%(objecttype) %(objectname) %(objectsize) %(rest)' | awk '$1 == "blob" && $3 > 1048576' | wc -l

# API timeout audit (NEW in 1.4.0)
echo "Fetch calls:" && grep -rn "fetch(" src/ --include="*.ts" --include="*.js" | wc -l
echo "Axios calls:" && grep -rn "axios\." src/ --include="*.ts" --include="*.js" | wc -l
echo "Timeout configs:" && grep -rn "timeout" src/ --include="*.ts" --include="*.js" | wc -l

# AbortController usage (NEW in 1.4.0)
grep -rn "AbortController\|signal:" src/ --include="*.ts" --include="*.js" | wc -l

# Dockerfile lint (NEW in 1.4.0)
npx dockerfilelint Dockerfile 2>/dev/null || echo "Install: npm install -g dockerfilelint"
```

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0 | Dec 2024 | Initial SOP created |
| 1.1.0 | Jan 2026 | Added quantitative metrics, enhanced scoring framework, framework-specific analysis, command templates, success criteria |
| 1.2.0 | Feb 2026 | Added TypeScript/compilation health checks, development environment assessment, SvelteKit-specific analysis, enhanced risk matrix with compilation errors and dev environment failures |
| 1.3.0 | Feb 2026 | Added parallel agent strategy, finding ID convention, delta audit variation, security as dedicated category (#10), memory safety metrics (addEventListener ratio), dark mode/theme coverage, design token adoption rate, accessibility media query checks, CSS framework-specific analysis, enhanced command templates, accessibility success criteria, pricing for delta audits, tips for parallel execution and previous audit inclusion |
| 1.4.0 | Mar 2026 | Added API & network resilience audit (#11), observability & logging audit (#12), internationalization assessment (#13), asset optimization audit (#14). Added license compliance checks (copyleft contamination), git hygiene analysis (secrets in history, large files), dead code/unused export detection, code complexity metrics, environment variable validation, Docker/container analysis commands, i18n readiness prompt variation, observability assessment prompt variation. Expanded framework-specific analysis for GraphQL, Python, and Go. Added 5th parallel agent for infrastructure & resilience. New finding ID prefixes: API-N, OBS-N, I18N-N, ASSET-N, LIC-N, GIT-N. Added success criteria for observability, API resilience. Multi-language support in pre-analysis commands (Python, Go, Rust). Enhanced security section with license compliance, git history hygiene, log scrubbing. |
