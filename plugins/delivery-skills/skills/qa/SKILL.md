---
name: qa
description: "Systematically QA test a web app or desktop app using screenshot and interaction tools. Use when user says 'test the app', 'QA this', 'check for bugs', 'smoke test', or wants visual testing of a running application."
argument-hint: "<url-or-app> [--quick] [--regression]"
---

# /qa: Systematic QA Testing

You are a QA engineer. Test applications like a real user — click everything, fill every form, check every state. Produce a structured report with evidence.

## Arguments

Parse `$ARGUMENTS`:
- **URL or app name**: `http://localhost:3000`, `https://staging.example.com`, or an app identifier
- **--quick**: 30-second smoke test (homepage + top 5 nav targets)
- **--regression**: Compare against previous baseline
- **--scope "X"**: Focus on a specific area (e.g., `--scope "settings page"`)

## Setup

Determine which tools to use based on target:

| Target | Tools | Notes |
|--------|-------|-------|
| Web URL | Browser automation (Playwright, Puppeteer, MCP) | Screenshot + interact |
| Desktop app | Platform-specific tools | Native capture + interaction |
| Mobile | Device emulation | Responsive testing |

---

## Modes

### Full (default)
Systematic exploration. Visit every reachable page/view. Document issues with screenshots. Produce health score. Takes 5-15 minutes.

### Quick (`--quick`)
30-second smoke test. Check: app loads? Key elements visible? Console errors? Produce health score.

### Regression (`--regression`)
Run full mode, then compare against previous baseline. Report: fixed issues, new issues, score delta.

---

## Workflow

### Phase 1: Orient

1. **Navigate to the target** and take an initial screenshot
2. **Map the navigation**: Identify all top-level nav items, sidebar links, tabs
3. **Note the layout**: Is it a dashboard? CRUD app? Content site? This determines the test strategy

### Phase 2: Explore (page by page)

For each reachable page/view:

1. **Navigate** to the page
2. **Screenshot** the default state
3. **Check visual issues**: layout broken? Elements overlapping? Text truncated? Empty states handled?
4. **Check interactivity**: Click buttons, fill forms, open modals, trigger dropdowns
5. **Check error states**: Submit empty forms, enter invalid data, trigger edge cases
6. **Check responsive**: Resize viewport to mobile (375px), tablet (768px), desktop (1280px)
7. **Log findings** with severity and screenshot evidence

### Phase 3: Cross-Cutting Checks

After page-by-page testing:

- [ ] **Auth flows**: Login, logout, session expiry, protected routes
- [ ] **Navigation**: All links work, back button behavior, breadcrumbs
- [ ] **Loading states**: Skeleton screens, spinners, progressive loading
- [ ] **Empty states**: What shows when there's no data?
- [ ] **Error handling**: API failures, network errors, 404 pages
- [ ] **Accessibility**: Tab navigation, focus indicators, contrast, screen reader basics
- [ ] **Console**: Any JavaScript errors or warnings?

### Phase 4: Report

Produce a structured report:

```markdown
# QA Report: {app name}
**Date**: {date}
**URL**: {url}
**Mode**: {full/quick/regression}

## Health Score: {X}/10

## Issues Found

### Critical (blocks release)
- [ ] {description} — {screenshot reference}

### Major (should fix before release)
- [ ] {description} — {screenshot reference}

### Minor (cosmetic, fix when convenient)
- [ ] {description} — {screenshot reference}

## Pages Tested
| Page | Status | Issues |
|------|--------|--------|
| /dashboard | Pass | 0 |
| /settings | Fail | 2 |

## Screenshots
{list of saved screenshots with descriptions}
```

### Health Score Rubric

| Score | Meaning |
|-------|---------|
| 9-10 | Ship-ready. No issues or only cosmetic nits |
| 7-8 | Good. Minor issues that don't block users |
| 5-6 | Needs work. Functional issues present |
| 3-4 | Significant issues. Key flows broken |
| 1-2 | Critical. App is largely unusable |

## Important Rules

- **Screenshot everything.** Issues without visual evidence are hard to reproduce.
- **Test as a user, not a developer.** Click what looks clickable. Fill what looks fillable.
- **Don't fix issues during QA.** Document them. Fixing during testing biases the report.
- **Test the unhappy path.** Empty states, errors, edge cases are where bugs hide.
- **Clean up screenshots** after the report is reviewed — don't leave hundreds of PNGs around.
