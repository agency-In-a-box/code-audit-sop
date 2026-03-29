# SOP: Design Excellence Audit - Amphibious & AIAB

**Version:** 1.2.0
**Date:** March 29, 2026
**Authors:** Clive Moore, Claude Code
**Purpose:** Systematic evaluation of design implementation quality, brand consistency, semantic markup excellence, visual craftsmanship, aesthetic distinctiveness, and visual regression integrity

---

## Design Philosophy

*"Design is not just what it looks like and feels like. Design is how it works."*

This SOP ensures every pixel serves a purpose, every animation feels inevitable, every HTML element is semantic, and every interaction respects the intelligence of our users. We audit not just for consistency, but for the ineffable quality that separates good design from design that changes industries.

### Core Amphibious Philosophy

> "This is the root. There is no hacking. Best practices only - you are a semantic purist and HTML naturalist."

- **Semantic HTML First**: Write meaningful, accessible markup that describes content structure
- **No Hacks**: Clean, standard-compliant CSS only - no workarounds or tricks
- **Minimal Classes**: Use as few classes as possible to achieve desired functionality
- **Mobile-First**: Design for mobile devices first, then enhance for larger screens
- **Performance**: Lean and fast - every byte counts

### Anti-AI-Slop Directive (NEW in 1.2.0)

> "If it looks like every other AI-generated interface, it has already failed."

Every design audit must evaluate whether the implementation exhibits generic, template-driven aesthetics or demonstrates genuine design intent. Predictable layouts, safe color choices, and default font stacks are failure modes, not starting points. The audit evaluates not just technical correctness but whether the design could only belong to Amphibious/AIAB.

---

## Brand Foundation: The Non-Negotiables

### Core Identity
- **Primary Color:** Pantone 144 (#ED8B00) - The confidence of autumn, the energy of innovation
- **Typography:** Avenir Next (primary), with fallback cascade to Futura PT, then system fonts
- **Spacing Quantum:** 4px baseline grid - the atomic unit of spatial harmony
- **Motion Signature:** 150-400ms with Apple-inspired cubic-bezier easing curves
- **Grid System:** 16-column flexbox for maximum flexibility
- **Namespace:** `.aiab-` prefix for all public CSS classes

### The Avenir Manifesto
Avenir isn't just a typeface - it's a philosophy of approachable sophistication:
- **Weights in Use:**
  - 300 (Light) - Whispers and annotations
  - 400 (Regular) - The voice of conversation
  - 500 (Medium) - Statements of intent
  - 600 (Demi Bold) - Emphasis without shouting
  - 700 (Bold) - Headlines that command attention

---

## Pre-Audit Preparation

### Design Assets Checklist
- [ ] All CSS/SCSS files from src/css/
- [ ] Design token definitions (design-tokens.css, breakpoints.css)
- [ ] Component library files (atoms/, molecules/, organisms/)
- [ ] Typography scales and line-height systems
- [ ] Animation and transition definitions
- [ ] Semantic HTML structure documentation
- [ ] Component hierarchy mapping
- [ ] Accessibility compliance reports
- [ ] Previous design audit reports (for delta comparison)
- [ ] Theme configuration files (theme tokens, prefers-color-scheme handlers) (NEW in 1.2.0)
- [ ] Visual regression baseline screenshots (NEW in 1.2.0)

### Environmental Setup
```bash
# Ensure consistent viewport testing
- Chrome/Edge at 1440x900 (MacBook Pro 14")
- Safari at 1512x982 (default window)
- Mobile at 390x844 (iPhone 14 Pro)
- Tablet at 820x1180 (iPad Air)
```

### Visual Regression Baseline Setup (NEW in 1.2.0)
```bash
# Playwright-based screenshot capture for regression testing
# Install: pip install playwright && playwright install chromium
# Capture baseline screenshots at each audit viewport
python -c "
from playwright.sync_api import sync_playwright
viewports = [
    {'name': 'desktop', 'width': 1440, 'height': 900},
    {'name': 'tablet', 'width': 820, 'height': 1180},
    {'name': 'mobile', 'width': 390, 'height': 844},
]
with sync_playwright() as p:
    browser = p.chromium.launch(headless=True)
    for vp in viewports:
        page = browser.new_page(viewport={'width': vp['width'], 'height': vp['height']})
        page.goto('http://localhost:3000')
        page.wait_for_load_state('networkidle')
        page.screenshot(path=f'audit-baseline-{vp[\"name\"]}.png', full_page=True)
    browser.close()
"
```

---

## The Master Design Audit Prompt

```
You are conducting a Design Excellence Audit with the critical eye of someone who spent
decades at Apple refining every pixel until it achieved inevitability, combined with
the semantic purity of a web standards advocate.

Evaluate this design system against these criteria:

1. **SEMANTIC HTML EXCELLENCE**
   Markup Quality Analysis:
   - Are HTML5 semantic elements used appropriately?
   - Verify proper heading hierarchy (h1-h6)
   - Check landmark roles (main, nav, aside, footer)
   - Document structure creates logical outline?
   - Form elements properly labeled and associated?
   - ARIA used to enhance, not replace semantics?
   - Skip links present on all pages?

   Semantic Patterns to Verify:
   - <main> for primary content (100% of pages)
   - <article> for self-contained content
   - <section> for thematic groupings
   - <aside> for complementary content
   - <nav> for navigation blocks (with aria-label)
   - <figure> and <figcaption> for media
   - <time> for temporal data (with datetime attribute)

2. **TYPOGRAPHY EXCELLENCE**
   Avenir Implementation Analysis:
   - Is Avenir Next properly loaded as primary font?
   - Verify complete weight range (300-700)
   - Check fallback cascade includes Futura PT
   - Line height creates proper rhythm (1.25 compact, 1.6 base, 2.0 relaxed)?
   - Letter-spacing adjustments for headlines (-0.02em to -0.03em)?
   - Measure vertical rhythm consistency against 4px baseline
   - Font-size scale follows tokenized progression?
   - Count hardcoded font-sizes vs tokenized (target: <20% hardcoded)

   Typography Metrics to Extract:
   - Base font size (should be 16px/1rem)
   - Scale progression (heading tokens h1-h6)
   - Line-height to font-size ratios
   - Paragraph spacing (should follow baseline grid)

   Typography Distinctiveness (NEW in 1.2.0):
   - Does the type system feel ownable or generic?
   - Are font pairings distinctive or default?
   - Does display typography create memorable moments?
   - Would a competitor's site look different, or interchangeable?

3. **COLOR HARMONY & PANTONE 144 IMPLEMENTATION**
   Brand Color Analysis:
   - Is #ED8B00 (Pantone 144) consistently used as primary?
   - Hover state: 10% lighter (#ff9500)
   - Active state: 11% darker (#d87a00)
   - Text variant: WCAG AA compliant (#a65e00, 7.6:1)
   - Alpha variants: 8 transparency levels defined?

   Palette Cohesion:
   - Secondary colors complement without competing
   - Grays follow systematic 11-step progression
   - Semantic colors (success, warning, error) harmonize with brand
   - Dark mode inversions maintain brand recognition (primary brightened)

   Color Conviction (NEW in 1.2.0):
   - Does the palette commit to a clear direction or hedge with safe neutrals?
   - Dominant colors with sharp accents outperform timid, evenly-distributed palettes
   - Are accent colors used with intention or scattered?
   - Does the color system create atmosphere or just label things?

4. **CLASS-BASED ORGANIZATION & NAMESPACE**
   Naming Convention Analysis:
   - BEM methodology applied (.aiab-block__element--modifier)?
   - .aiab- namespace prefix on all public classes?
   - State classes prefixed (is-active, has-error)?
   - Utility classes minimized and semantic?
   - Count BEM element (__) and modifier (--) patterns

   Class Hierarchy:
   - Component classes (primary structure)
   - Element classes (component parts)
   - Modifier classes (variations)
   - State classes (dynamic states)
   - NO presentational class names

5. **SPATIAL RELATIONSHIPS & GRID DISCIPLINE**
   4px Baseline Grid Adherence:
   - Count violations of 4px grid (margins, padding not divisible by 4)
   - Verify spacing token scale (4, 8, 16, 24, 32px)
   - Check component spacing consistency
   - Measure white space as percentage of viewport
   - Target: >95% 4px grid compliance

   16-Column Grid Implementation:
   - Flexbox grid properly implemented
   - Column classes (aiab-col-1 through aiab-col-16)
   - Responsive modifiers
   - Gutter consistency (20px/5 grid units)
   - Nested grids maintain alignment

   Spatial Composition (NEW in 1.2.0):
   - Does the layout use space as a design element or just fill it?
   - Are there intentional asymmetries or grid-breaking moments that create visual tension?
   - Does negative space guide the eye or create dead zones?
   - Is density controlled — either generous whitespace OR intentional information density?
   - Do overlapping elements, diagonal flows, or unconventional groupings appear where appropriate?

6. **MOTION & MICRO-INTERACTIONS**
   Animation Sophistication:
   - Duration tokens defined (150ms instant, 250ms fast, 400ms slow)?
   - Easing uses named cubic-bezier curves (not linear for UI transitions)?
   - Transform animations over position changes (GPU-accelerated)?
   - prefers-reduced-motion universally respected?
   - Count transitions clustering at best-practice values (200-400ms)

   Interaction Feedback:
   - Hover states respond within 150ms
   - Active states provide immediate feedback
   - Loading states maintain visual continuity
   - Error states guide without alarming

   Motion Orchestration (NEW in 1.2.0):
   - Do page-load reveals use staggered animation-delay for sequenced entry?
   - Are scroll-triggered animations purposeful or decorative noise?
   - Do hover states surprise or merely confirm?
   - Is there a single, well-orchestrated moment that creates delight?
   - CSS-only solutions preferred over JS animation libraries where possible

7. **COMPONENT CRAFTSMANSHIP**
   Semantic Component Structure:
   - Components use appropriate semantic wrappers
   - Internal hierarchy uses semantic elements
   - Card components use article or section
   - Navigation components use nav element
   - Form components properly structured with fieldsets

   Button Excellence:
   - Minimum touch target 44x44px (iOS HIG)
   - Border radius consistency (canonical scale: 4px, 8px, 12px, 16px, pill)
   - Shadow progression (xs → sm → md → lg → xl → 2xl)
   - Text remains centered during state changes

   Design System Completeness:
   - Skeleton loaders with shimmer animation
   - Spinner variants (ring, dots, pulse, wave)
   - Error state design (4-state: error/success/warning/info)
   - Empty state patterns

8. **VISUAL HIERARCHY & INFORMATION DESIGN**
   Hierarchical Markup Patterns:
   - Parent-child relationships clear in HTML
   - Visual hierarchy matches semantic hierarchy
   - Related content grouped in semantic containers
   - Design pairs (label-input, header-content) properly associated

   Typographic Hierarchy:
   - Minimum 3, maximum 6 font sizes per view
   - Weight creates emphasis, not just size
   - Color reinforces hierarchy (ink → ink-light → ink-lighter)
   - Spacing between hierarchy levels follows scale

9. **RESPONSIVE DESIGN INTELLIGENCE**
   Breakpoint Philosophy:
   - Content drives breakpoints, not devices
   - Typography scales proportionally
   - Spacing tightens appropriately on mobile
   - Touch targets grow on touch devices
   - @custom-media tokens used consistently

   Mobile-First Implementation:
   - Base styles assume mobile
   - Progressive enhancement via min-width queries
   - No horizontal scroll at any viewport
   - Thumb-reachable interaction zones
   - Track min-width vs max-width query ratio

10. **ACCESSIBILITY AS DESIGN PRINCIPLE**
    Visual Accessibility:
    - WCAG AAA contrast where possible (7:1)
    - Focus indicators visible and beautiful (:focus-visible with branded color)
    - Color not sole indicator of state
    - prefers-reduced-motion respected (count files)
    - prefers-contrast: high supported (count files)
    - Print styles comprehensive

    Semantic Accessibility:
    - Proper heading structure for screen readers
    - Form labels associated with inputs
    - Buttons vs links used appropriately
    - Landmark roles guide navigation
    - Alt text meaningful and descriptive

11. **DARK MODE VISUAL QUALITY**
    Theme Architecture:
    - Complete variable inversion (not just black background)
    - Elevation hierarchy (lighter = higher in dark mode)
    - Brand color brightened for dark backgrounds
    - Shadow opacity increased proportionally (3-5x for equivalent depth)
    - Smooth theme transition with reduced-motion respect
    - Component coverage percentage (target: 100%)

    Dark Mode Metrics:
    - Count components WITH explicit dark mode styles
    - Count components WITHOUT dark mode (hardcoded backgrounds)
    - Verify dark mode maintains brand recognition
    - Check dual trigger support (prefers-color-scheme + data-theme)

12. **DESIGN TOKEN ADOPTION**
    Token Coverage:
    - Total tokens defined (target: 100+ for mature system)
    - var() usage count across all CSS
    - Adoption rate percentage (target: >85%)
    - Hardcoded values that should be tokens

    Token Categories to Audit:
    - Colors (base + alpha variants)
    - Typography (family, size, weight, line-height, letter-spacing)
    - Spacing (following baseline grid)
    - Borders (radius scale, width)
    - Shadows (elevation scale + dark mode variants)
    - Motion (duration tokens, easing curves)

13. **AESTHETIC DISTINCTIVENESS & AI-SLOP DETECTION** (NEW in 1.2.0)
    Generic Pattern Detection:
    - Does the design use default/overused font stacks (Inter, Roboto, system-ui)?
    - Are color schemes cliched (purple gradients on white, blue-to-teal)?
    - Do layouts follow predictable component-library patterns without customization?
    - Are backgrounds flat solid colors where atmosphere would serve the design?
    - Do card grids, hero sections, and CTAs look interchangeable with any SaaS template?

    Atmosphere & Depth:
    - Do backgrounds create depth (gradient meshes, noise textures, layered transparencies)?
    - Are there contextual effects that match the aesthetic (grain overlays, dramatic shadows)?
    - Does the design use decorative borders, custom cursors, or geometric patterns where appropriate?
    - Is there visual texture or is everything flat and clinical?

    Design Intent:
    - Can you articulate the aesthetic direction in 2-3 words (e.g., "brutally minimal," "warm industrial")?
    - If you can't name it, it doesn't have one — that's a finding
    - Does the design commit to its direction or hedge with safe choices?
    - Is there a single unforgettable element someone would remember after closing the browser?
    - Would removing the logo make the design unrecognizable, or does brand identity permeate the visual language?

    Scoring Criteria:
    - 10: Unmistakably ownable — could only be this brand
    - 7-9: Clear direction with moments of distinction
    - 4-6: Competent but interchangeable with competitors
    - 1-3: Template-driven, no discernible design point of view

14. **THEME SYSTEM ARCHITECTURE** (NEW in 1.2.0)
    Theme Infrastructure:
    - Are themes defined as complete token sets (colors, fonts, spacing) or just color swaps?
    - Is there a single source of truth for theme definitions?
    - Can new themes be added without modifying component code?
    - Is theme switching handled via CSS custom properties, data attributes, or both?

    Theme Consistency:
    - Does each theme maintain brand recognition across all components?
    - Are font pairings (header + body) defined per theme or hardcoded globally?
    - Do accent colors cycle coherently within each theme?
    - Is contrast and readability verified per theme (not just the default)?

    Multi-Theme Readiness:
    - Can the system support beyond light/dark (e.g., high-contrast, seasonal, client-branded)?
    - Are theme tokens isolated from component logic?
    - Is there a theme preview or showcase mechanism for stakeholder review?
    - Theme fallback behavior when custom theme fails to load?

15. **VISUAL REGRESSION INTEGRITY** (NEW in 1.2.0)
    Automated Verification:
    - Are baseline screenshots captured at each audit viewport (desktop, tablet, mobile)?
    - Is Playwright (or equivalent) configured for headless screenshot comparison?
    - Are visual diffs generated between audit cycles?
    - Are critical user flows captured as screenshot sequences (not just static pages)?

    Regression Coverage:
    - Key pages: homepage, dashboard, forms, error states, empty states
    - Component states: default, hover, active, focus, disabled, loading, error
    - Theme variants: light mode, dark mode, high-contrast (if supported)
    - Viewport coverage: all four audit viewports

    Integration:
    - Can visual regression run in CI/CD pipeline?
    - Are screenshot baselines versioned alongside code?
    - Is pixel-diff threshold configured (recommended: 0.1% tolerance)?
    - Are flaky screenshots (animation timing, font rendering) handled with masking?

SCORING RUBRIC:
Rate each category 1-10, then provide overall Design Excellence Score.

10 - Apple-level craftsmanship with W3C compliance
9  - Industry-leading quality
8  - Professional, polished
7  - Good with rough edges
6  - Functional but uninspired
5  - Mediocre execution
4  - Significant issues
3  - Amateur implementation
2  - Fundamentally broken
1  - Complete failure

Provide specific examples with HTML elements, CSS selectors and property values.
Assign finding IDs (e.g., TYPO-1, COLOR-2, DARK-3, ASLOP-1, THEME-2, VREG-1) for tracking across audits.
Identify the single most impactful improvement that would elevate the design.
```

---

## Parallel Agent Strategy

For large design systems, split the audit across parallel agents:

**Agent 1 — Design Tokens & Typography**: Extract all tokens, audit font system, check spacing grid compliance, measure color palette completeness, catalog motion tokens.

**Agent 2 — Semantic HTML & Components**: Audit HTML quality across pages, check BEM naming, evaluate component craftsmanship (buttons, cards, modals, forms), measure visual hierarchy.

**Agent 3 — Visual Polish & Dark Mode**: Evaluate dark mode quality, focus indicators, shadow system, border radius consistency, icon system, loading/error states, print styles, high contrast mode.

**Agent 4 — Aesthetic Distinctiveness & Theme Architecture** (NEW in 1.2.0): Run the AI-slop detection audit, evaluate atmosphere and depth, audit theme system infrastructure, verify theme consistency across components, assess design intent and brand ownability.

**Agent 5 — Visual Regression Capture** (NEW in 1.2.0): Execute Playwright screenshot capture across all viewports, generate visual diffs against previous audit baselines, flag pixel-level regressions, document animation and loading state captures.

Compile all agent results into the final report with weighted scoring.

---

## Specialized Design Audits

### Semantic HTML Deep Dive
```
Conduct a semantic markup focused audit:

1. Document Structure Analysis:
   - Proper DOCTYPE and lang attribute?
   - Meta tags complete (charset, viewport, description)?
   - Heading hierarchy creates logical outline?
   - Landmark elements define page regions?
   - Skip links present on every page?

2. Content Semantics:
   - Lists use ul/ol/dl appropriately?
   - Tables include thead/tbody/tfoot?
   - Forms use fieldset/legend for grouping?
   - Quotes use blockquote/cite correctly?

3. Interactive Elements:
   - Buttons vs anchors used correctly?
   - Form inputs have associated labels?
   - Required fields marked appropriately?
   - Error messages associated with inputs?

4. Media Elements:
   - Images have meaningful alt attributes?
   - Figures include figcaptions?
   - Video/audio have text alternatives?
   - SVGs have title/desc elements?

Rate semantic HTML execution: X/10
Most impactful semantic improvement?
```

### Typography & Avenir Audit
```
Conduct a typography-focused design audit:

1. Measure and document:
   - Every font-size in use (should be < 8 unique sizes)
   - Every line-height value (should follow consistent ratio)
   - Every font-weight in use (should be 3-5 weights max)
   - Letter-spacing adjustments (headlines should be tighter)
   - Text color variations (should be 3-4 max)
   - Tokenized vs hardcoded ratio

2. Avenir implementation check:
   - @font-face declarations properly configured?
   - WOFF2 format prioritized for performance?
   - Font-display: swap for better loading UX?
   - Subset fonts to reduce file size?

3. Reading experience:
   - Measure characters per line (45-75 optimal)
   - Line height creates rivers of white space?
   - Paragraph spacing follows baseline grid?
   - Pull quotes or emphasis properly styled?

4. Responsive typography:
   - Font sizes scale appropriately (clamp() or calc())?
   - Line heights adjust for screen size?
   - Headlines break elegantly?

Rate typography execution: X/10
Single improvement for biggest impact?
```

### Dark Mode Quality Audit
```
Evaluate dark mode design quality:

1. Color Inversion Quality:
   - Does dark mode use proper surface hierarchy (not just black)?
   - Are elevation levels represented by lighter shades?
   - Does the primary brand color adapt for dark backgrounds?
   - Are shadow opacities increased for equivalent visual depth?

2. Component Coverage:
   - Count components with explicit dark mode styles
   - Count components with hardcoded light-mode colors
   - Identify components that rely on token-based auto-adaptation
   - Calculate dark mode coverage percentage

3. Theme Transition:
   - Is the light/dark transition smooth?
   - Is prefers-reduced-motion respected during transitions?
   - Does localStorage persistence work correctly?
   - Is there flash of incorrect theme on load?

4. Visual Harmony:
   - Do dark surfaces feel warm or cold? (Apple-inspired warm preferred)
   - Are text hierarchy levels maintained?
   - Do interactive elements remain visible and inviting?
   - Does the design feel intentional, not just inverted?

Rate dark mode quality: X/10
Most impactful dark mode improvement?
```

### Class Organization & BEM Audit
```
Evaluate class-based organization:

1. Naming Convention Consistency:
   - BEM methodology followed (.aiab-block__element--modifier)?
   - .aiab- namespace prefix on all public classes?
   - Component boundaries clear?
   - No presentational class names?
   - State classes properly prefixed?

2. Class Hierarchy:
   - Parent components identifiable?
   - Child elements scoped to parents?
   - Modifiers non-destructive?
   - States toggleable without breaking?

3. Semantic Class Names:
   - Classes describe purpose not appearance?
   - Component names meaningful?
   - Avoid generic names (box, wrapper)?
   - Consistent vocabulary across codebase?

4. CSS Architecture:
   - Specificity kept low?
   - No ID selectors for styling?
   - Zero !important declarations?
   - Cascade used appropriately?
   - Custom properties for variations?

Rate class organization: X/10
Most important refactoring needed?
```

### Animation & Motion Audit
```
Evaluate motion design quality:

1. Catalog every transition/animation:
   - Duration tokens used (150ms, 250ms, 400ms)?
   - Easing uses named cubic-bezier curves?
   - Properties animated (transform > position)?
   - Trigger (hover, click, scroll, load)?

2. Motion principles:
   - Does motion have purpose or is it decoration?
   - Do related elements animate together?
   - Does timing create rhythm or chaos?
   - Are entrance/exit animations balanced?

3. Performance impact:
   - GPU-accelerated properties used (transform, opacity)?
   - will-change hints appropriate?
   - Animation frame rate consistent?
   - Battery impact on mobile considered?

4. Accessibility:
   - prefers-reduced-motion respected in how many files?
   - Essential animations preserved?
   - Motion doesn't trigger vestibular issues?

Rate motion design: X/10
Most impactful animation to add or remove?
```

### Pantone 144 Brand Expression
```
Analyze brand color implementation:

1. Pantone 144 (#ED8B00) usage:
   - Used consistently as primary brand color?
   - Appears in logo, CTAs, and key actions?
   - Hover/active states follow systematic relationships?
   - Text variant (#a65e00) meets WCAG AA (7.6:1)?
   - Alpha variants defined (8 levels)?

2. Color harmony:
   - Secondary colors complement orange?
   - Avoided competing warm colors (reds, yellows)?
   - Cool colors (blues, greens) create balance?
   - Neutrals allow brand color to pop?

3. Emotional impact:
   - Does orange convey energy and innovation?
   - Professional without being corporate?
   - Warm without being overwhelming?
   - Confident without being aggressive?

4. Implementation quality:
   - CSS variables used for consistency?
   - Hover: 10% lighter, Active: 11% darker?
   - Dark mode: brightened to #ff9500?
   - Print styles preserve brand colors?

Rate brand expression: X/10
Single change to strengthen brand identity?
```

### Aesthetic Distinctiveness Audit (NEW in 1.2.0)
```
Evaluate whether the design transcends generic AI/template aesthetics:

1. AI-Slop Pattern Scan:
   - List every instance of: Inter, Roboto, Arial, system-ui as primary font
   - Flag purple-gradient-on-white or blue-to-teal color schemes
   - Identify cookie-cutter hero/card/CTA patterns from component libraries
   - Count solid-color backgrounds where texture/depth would serve better
   - Flag any "corporate Memphis" illustration style

2. Atmosphere Evaluation:
   - Rate background treatment (flat=1, textured=5, atmospheric=10)
   - Catalog depth-creating techniques in use (shadows, layers, gradients, noise, grain)
   - Identify missed opportunities for visual texture
   - Evaluate whether the design has a "temperature" (warm/cool/neutral)

3. Memorability Test:
   - Close the browser. What do you remember? Write it down.
   - If the answer is "nothing specific" — that's a critical finding (ASLOP-MEMO)
   - Identify the single most distinctive visual element
   - Could this design belong to a different brand with a logo swap? (If yes: ASLOP-GENERIC)

4. Design Direction Coherence:
   - Name the aesthetic direction in 2-3 words
   - Does every component reinforce that direction?
   - Are there components that break the aesthetic without justification?
   - Is the direction documented or only implicit?

Rate aesthetic distinctiveness: X/10
Single change to make the design unmistakably ownable?
```

### Theme System Audit (NEW in 1.2.0)
```
Evaluate theme architecture and multi-theme readiness:

1. Theme Token Structure:
   - Are theme definitions complete (colors, fonts, spacing, shadows)?
   - Are theme tokens separate from component tokens?
   - Can a new theme be created by copying and modifying one file?
   - Are semantic token names used (--color-surface, --color-accent) vs raw values?

2. Theme Switching Mechanism:
   - CSS custom properties as the switching layer?
   - data-theme attribute on root element?
   - prefers-color-scheme media query as initial trigger?
   - No flash of wrong theme on page load (FOIT/FOUC equivalent)?
   - Theme preference persisted (localStorage or server-side)?

3. Cross-Theme Consistency:
   - Run the full component library in each theme — are any broken?
   - Do all themes maintain WCAG AA contrast ratios?
   - Are font pairings appropriate per theme or globally hardcoded?
   - Do illustrations/icons adapt to theme or create contrast issues?

4. Extensibility:
   - Can client-branded themes be generated from a base template?
   - Is there a theme showcase or preview page?
   - Are theme tokens documented for third-party consumers?
   - Fallback behavior when theme definition is incomplete?

Rate theme system: X/10
Most critical theme architecture improvement?
```

### Visual Regression Audit (NEW in 1.2.0)
```
Evaluate visual regression testing infrastructure:

1. Baseline Coverage:
   - Are baseline screenshots captured for all key pages?
   - All four audit viewports represented (desktop, tablet, mobile, Safari)?
   - Component states captured (default, hover, active, focus, disabled, error, loading)?
   - Both light and dark mode baselines exist?

2. Diff Generation:
   - Are pixel-level diffs generated between audit cycles?
   - What tolerance threshold is configured? (Recommended: 0.1%)
   - Are flaky areas masked (animations, dynamic content, timestamps)?
   - Is font rendering variance across OS/browser accounted for?

3. Critical Flow Coverage:
   - Homepage → navigation → key conversion path screenshotted end-to-end?
   - Form flows (empty → filled → error → success) captured?
   - Modal/overlay states captured with backdrop?
   - Loading/skeleton states captured mid-transition?

4. CI/CD Integration:
   - Can regression suite run on every PR?
   - Are baseline images versioned in the repository?
   - Is there a review workflow for intentional visual changes?
   - Average regression suite runtime? (Target: <5 minutes)

Rate visual regression maturity: X/10
Most critical gap in visual verification?
```

---

## Design Metrics to Extract

### Quantitative Measurements
```bash
# Count semantic HTML5 elements
grep -r "<main\|<article\|<section\|<aside\|<nav\|<header\|<footer" *.html | wc -l

# Audit heading hierarchy
grep -r "<h[1-6]" *.html | sort | uniq -c

# Count unique font-sizes (hardcoded vs tokenized)
echo "Hardcoded:" && grep -r "font-size:" src/css/ | grep -v "var(--" | sort -u | wc -l
echo "Tokenized:" && grep -r "font-size:.*var(--" src/css/ | wc -l

# Find non-grid spacing (values not divisible by 4px)
grep -r "padding:\|margin:" src/css/ | grep -v -E "(4|8|12|16|20|24|32|48|64|96)px"

# Class naming audit
grep -r "class=" *.html | grep -o 'class="[^"]*"' | sort | uniq -c | sort -rn

# BEM naming patterns
echo "BEM elements:" && grep -r "__" src/css/ --include="*.css" | wc -l
echo "BEM modifiers:" && grep -r "\-\-" src/css/ --include="*.css" | grep "aiab" | wc -l

# Animation duration audit
grep -r "transition:\|animation:" src/css/ | grep -o "[0-9.]\+[m]*s" | sort | uniq -c

# Design token adoption
echo "Token definitions:" && grep -r "^  --" src/css/tokens/ --include="*.css" | wc -l
echo "Token usages:" && grep -r "var(--" src/css/ --include="*.css" | wc -l

# Dark mode coverage
echo "Files with dark mode:" && grep -rl "prefers-color-scheme\|data-theme.*dark" src/css/ --include="*.css" | wc -l
echo "Total CSS files:" && find src/css/ -name "*.css" | wc -l

# Shadow system
echo "Unique shadows:" && grep -r "box-shadow:" src/css/ --include="*.css" | wc -l

# Border radius consistency
grep -r "border-radius:" src/css/ --include="*.css" | grep -o "[0-9.]*rem\|[0-9.]*px\|[0-9]*%" | sort | uniq -c | sort -rn

# Accessibility media queries
echo "prefers-reduced-motion:" && grep -rl "prefers-reduced-motion" src/css/ --include="*.css" | wc -l
echo "prefers-contrast:" && grep -rl "prefers-contrast" src/css/ --include="*.css" | wc -l
echo "print:" && grep -rl "@media print" src/css/ --include="*.css" | wc -l

# Find magic numbers (hardcoded values that should be variables)
grep -r "[0-9]\+px" src/css/ | grep -v "var(--"

# AI-slop font detection (NEW in 1.2.0)
echo "Generic font usage:" && grep -rn "Inter\|Roboto\|Arial\|system-ui" src/css/ --include="*.css" | wc -l

# Background depth audit (NEW in 1.2.0)
echo "Solid backgrounds:" && grep -r "background:\s*#\|background-color:\s*#" src/css/ --include="*.css" | grep -v "gradient\|rgba\|hsla" | wc -l
echo "Gradient/texture backgrounds:" && grep -r "gradient\|noise\|texture\|backdrop-filter" src/css/ --include="*.css" | wc -l

# Theme token isolation (NEW in 1.2.0)
echo "Theme-scoped tokens:" && grep -r "\[data-theme\]\|prefers-color-scheme" src/css/ --include="*.css" | wc -l
echo "Hardcoded theme values:" && grep -r "background:\s*#fff\|background:\s*#000\|background:\s*white\|background:\s*black" src/css/ --include="*.css" | wc -l
```

### Qualitative Assessments
1. **Semantic Clarity** - Can you understand the content structure without CSS?
2. **First Impression** - What emotion does the design evoke in 2 seconds?
3. **Brand Alignment** - Does it feel like Amphibious/AIAB?
4. **Craftsmanship** - Are details polished or rough?
5. **Innovation** - Does anything surprise or delight?
6. **Accessibility** - Can it be navigated without a mouse?
7. **Memorability** - What sticks after closing the browser?
8. **Dark Mode Quality** - Does dark mode feel intentional or tacked on?
9. **Aesthetic Ownability** - Could this only be AIAB, or could it be anyone? (NEW in 1.2.0)
10. **Atmosphere** - Does the design have depth and texture, or is it flat? (NEW in 1.2.0)

---

## Post-Audit Deliverables

### 1. Design Excellence Report (Executive)
- Overall Design Score (X/10) with category breakdown (now 15 categories)
- Semantic HTML compliance rating
- Brand consistency rating
- Dark mode coverage percentage
- Design token adoption rate
- Aesthetic distinctiveness score (NEW in 1.2.0)
- Theme system maturity rating (NEW in 1.2.0)
- Visual regression coverage percentage (NEW in 1.2.0)
- Top 3 wins to preserve
- Top 3 issues to address
- Single highest-impact improvement
- Delta comparison (if previous audit exists)

### 2. Technical Design Audit (Detailed)
- Complete findings by category with finding IDs
- Specific HTML elements and CSS selectors
- Measurement data and metrics
- Code examples of improvements
- Semantic markup recommendations
- Dark mode gap analysis
- Design token coverage report
- AI-slop pattern inventory (NEW in 1.2.0)
- Theme architecture assessment (NEW in 1.2.0)
- Visual regression diff report with screenshots (NEW in 1.2.0)

### 3. Design System Evolution Roadmap

**Phase 1: Quick Wins (Days 1-2)**
- Fix spacing grid violations
- Tokenize hardcoded values
- Standardize BEM inconsistencies
- Add missing ARIA attributes

**Phase 2: Visual Foundation (Week 1)**
- Fix typography scale inconsistencies
- Implement proper Avenir loading
- Establish consistent spacing variables
- Correct Pantone 144 usage
- Align to 16-column grid

**Phase 3: Dark Mode Expansion (Week 2)**
- Add dark mode to all uncovered components
- Consolidate duplicate dark mode tokens
- Test all components in dark mode
- Verify shadow/elevation hierarchy

**Phase 4: Component Architecture (Week 3)**
- Refactor class naming to BEM
- Create semantic component patterns
- Standardize state classes
- Document component hierarchy
- Build reusable patterns

**Phase 5: Aesthetic Differentiation (Week 4)** (UPDATED in 1.2.0)
- Address AI-slop findings — replace generic patterns with ownable design choices
- Add atmospheric depth to flat backgrounds
- Implement signature micro-interactions
- Establish and document the aesthetic direction
- Create the "unforgettable element" for brand memorability

**Phase 6: Theme System & Visual Regression (Week 5)** (NEW in 1.2.0)
- Isolate theme tokens from component logic
- Build theme preview/showcase mechanism
- Configure Playwright visual regression suite
- Capture initial baselines across all viewports and themes
- Integrate regression suite into CI/CD pipeline

**Phase 7: Polish & Performance (Week 6)** (RENUMBERED)
- Motion orchestration refinements
- Loading state design
- Error state refinement
- High contrast mode expansion
- Performance optimization

---

## The Designer's Semantic Checklist

Before declaring design complete:

### HTML Structure
- [ ] Every page has one `<main>` element
- [ ] Navigation wrapped in `<nav>` with aria-label
- [ ] Sidebar content in `<aside>`
- [ ] Self-contained content in `<article>`
- [ ] Thematic groups in `<section>`
- [ ] Only one `<h1>` per page
- [ ] Heading hierarchy never skips levels
- [ ] Forms use `<fieldset>` and `<legend>`
- [ ] All inputs have associated `<label>`
- [ ] Lists use appropriate `<ul>`, `<ol>`, or `<dl>`
- [ ] Skip links on every page

### CSS Architecture
- [ ] Classes follow BEM with .aiab- namespace
- [ ] No presentational class names
- [ ] State classes prefixed (is-, has-)
- [ ] Component classes semantic
- [ ] Utility classes minimal
- [ ] CSS custom properties for all tokens
- [ ] No magic numbers
- [ ] Specificity kept low
- [ ] Zero !important declarations

### Design Tokens
- [ ] Colors tokenized (base + alpha + dark mode)
- [ ] Typography tokenized (family, size, weight, line-height, letter-spacing)
- [ ] Spacing follows 4px baseline grid
- [ ] Border radius scale defined
- [ ] Shadow elevation scale defined
- [ ] Motion tokens (durations + easing curves)
- [ ] Token adoption rate >85%

### Visual Design
- [ ] Every element aligns to 4px grid
- [ ] Avenir loads properly with all weights
- [ ] Pantone 144 (#ED8B00) appears consistently
- [ ] 16-column grid implemented correctly
- [ ] Animations feel inevitable, not decorative
- [ ] Touch targets meet 44px minimum
- [ ] Focus states visible and beautiful
- [ ] Loading states maintain continuity
- [ ] Error states guide without alarming
- [ ] Dark mode feels intentional (not just inverted)
- [ ] Dark mode coverage: 100% of components

### Aesthetic Distinctiveness (NEW in 1.2.0)
- [ ] Design direction is nameable in 2-3 words
- [ ] No generic font stacks (Inter, Roboto, Arial) as primary
- [ ] Backgrounds use depth/texture where appropriate (not all flat solids)
- [ ] At least one "unforgettable element" exists
- [ ] Design is not interchangeable with a logo swap
- [ ] No corporate Memphis or stock illustration patterns

### Theme System (NEW in 1.2.0)
- [ ] Theme tokens isolated from component tokens
- [ ] Theme switching via CSS custom properties + data-theme attribute
- [ ] No flash of wrong theme on load
- [ ] All themes pass WCAG AA contrast
- [ ] Theme preview mechanism available

### Visual Regression (NEW in 1.2.0)
- [ ] Baseline screenshots captured at all audit viewports
- [ ] Playwright (or equivalent) configured for automated capture
- [ ] Pixel-diff threshold set (0.1% recommended)
- [ ] Critical user flows covered as screenshot sequences
- [ ] Baselines versioned alongside code

### Accessibility
- [ ] Keyboard navigation works throughout
- [ ] Screen reader experience tested
- [ ] Color contrast meets WCAG AA minimum
- [ ] Focus indicators clearly visible (:focus-visible)
- [ ] No information conveyed by color alone
- [ ] Alt text meaningful and descriptive
- [ ] ARIA enhances, doesn't replace semantics
- [ ] prefers-reduced-motion respected
- [ ] prefers-contrast: high supported
- [ ] Print styles comprehensive

---

## Finding ID Convention

Assign IDs to all findings for tracking across audits:

| Prefix | Category |
|--------|----------|
| TYPO-N | Typography |
| COLOR-N | Color & Brand |
| SPACE-N | Spacing & Grid |
| MOTION-N | Motion & Animation |
| COMP-N | Component Craftsmanship |
| HTML-N | Semantic HTML |
| DARK-N | Dark Mode |
| A11Y-D-N | Accessibility (Design) |
| RESP-N | Responsive Design |
| TOKEN-N | Design Token Adoption |
| ASLOP-N | Aesthetic Distinctiveness / AI-Slop (NEW in 1.2.0) |
| THEME-N | Theme System Architecture (NEW in 1.2.0) |
| VREG-N | Visual Regression (NEW in 1.2.0) |

---

## Design Principles for Amphibious/AIAB

### 1. Semantic First
HTML tells the story. CSS makes it beautiful. JavaScript makes it interactive. Never confuse their roles.

### 2. Clarity Through Restraint
Remove everything that doesn't serve the user's goal. Then remove half of what remains.

### 3. Motion with Purpose
Every animation should feel like it couldn't happen any other way. Physics over flourish.

### 4. Confidence Through Color
Pantone 144 is our signature - use it boldly but strategically. It's the exclamation point, not the paragraph.

### 5. Typography as Interface
Avenir isn't just for reading - it's for wayfinding, hierarchy, and emotion. Let it breathe.

### 6. Space as Luxury
White space isn't empty - it's potential. Use it generously to create focus and elegance.

### 7. Grid as Foundation
The 16-column grid isn't a constraint - it's a framework for infinite possibilities.

### 8. Classes with Meaning
Every class name should explain its purpose to a developer who's never seen it before.

### 9. Consistency Without Monotony
Systems create harmony, but moments of intentional deviation create delight.

### 10. Accessible by Default
Great design works for everyone. Accessibility isn't a feature - it's the foundation.

### 11. Dark Mode as First-Class Citizen
Dark mode isn't an afterthought. Every component must work beautifully in both themes from day one.

### 12. Tokens as Truth
Design decisions live in tokens, not in component files. Change the token, change the world.

### 13. Ownability Over Correctness (NEW)
A technically correct design that could belong to anyone has failed. Distinction is a requirement, not a bonus.

### 14. Atmosphere is Architecture (NEW)
Depth, texture, and temperature are structural design decisions — not decoration applied after the fact.

---

## Design Audit Pricing

| Tier | Scope | Deliverables | Price |
|------|-------|--------------|-------|
| Quick Review | Single page/component | 1-page visual critique + semantic audit | $200 |
| Standard Audit | Full application | Complete report + roadmap + semantic analysis | $750 |
| Excellence Package | Multi-platform + brand | Report + roadmap + design system + component library | $1,500+ |
| Delta Audit | Repeat audit with comparison | Delta report + updated roadmap | $400 |
| Regression Setup | Visual regression infrastructure | Playwright config + baselines + CI integration | $500 (NEW) |

---

## Version History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.2.0 | Mar 29, 2026 | Clive Moore, Claude Code | Added Aesthetic Distinctiveness & AI-Slop Detection (#13) sourced from Anthropic frontend-design skill anti-pattern guidelines; added Theme System Architecture (#14) sourced from Anthropic theme-factory skill; added Visual Regression Integrity (#15) sourced from Anthropic webapp-testing skill with Playwright integration; added Anti-AI-Slop Directive to Design Philosophy; new specialized audits for aesthetics, themes, and visual regression; new finding ID prefixes (ASLOP, THEME, VREG); expanded parallel agent strategy to 5 agents; new quantitative metrics (AI-slop font detection, background depth, theme token isolation); new design principles #13 Ownability Over Correctness and #14 Atmosphere is Architecture; new checklist sections; Regression Setup pricing tier; expanded roadmap to 7 phases |
| 1.1.0 | Feb 27, 2026 | Claude Code | Added dark mode quality audit (#11), design token adoption (#12), parallel agent strategy, finding ID convention, delta audit pricing, quantitative metric commands for tokens/dark mode/shadows/border-radius, enhanced master prompt with namespace/token/dark mode criteria, dark mode as design principle, new specialized dark mode audit, expanded checklist with token and dark mode sections |
| 1.0.05 | Jan 22, 2026 | Design Excellence Team | Enhanced semantic HTML requirements, added class organization patterns, integrated Amphibious philosophy |
| 1.0.04 | Jan 15, 2026 | Claude Code | Added Storybook documentation standards |
| 1.0.03 | Dec 2024 | Clive Moore | Initial SOP with focus on Amphibious/AIAB brand standards |
| 1.0.02 | Nov 2024 | Design Team | Typography and motion guidelines |
| 1.0.01 | Oct 2024 | Initial Team | Foundation document |

---

*"Simplicity is the ultimate sophistication. But achieving simplicity requires understanding complexity. Semantic HTML is the foundation of that understanding."*

**Remember:** We're not just checking boxes. We're crafting experiences that respect our users' time, intelligence, and aesthetic sensibility. Every element is semantic. Every class has purpose. Every pixel is a promise. Every animation is an interaction. Every color is communication. Every theme is intentional. Every design is ownable.

Design like the user is watching over your shoulder. Code like a screen reader is parsing every element. Because they are.
