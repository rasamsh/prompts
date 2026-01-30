# Browser JS → Playwright BDD Migration Prompts

VS Code Copilot prompts for migrating **Browser JavaScript** to **Playwright BDD**.

**Important**: Uses `playwright-bdd` package, NOT `@cucumber/cucumber`.

---

## 🚀 Quick Start

### Analyze Browser JS Tests
```
@workspace Analyze all test files and identify Browser JS patterns:
- document.getElementById, querySelector, querySelectorAll
- jQuery $() selectors  
- DOM property access (.value, .innerText, .innerHTML)
- DOM actions (.click(), .focus(), .submit())
- setTimeout, setInterval usage
- window.location navigation
- localStorage/sessionStorage usage

Create a migration report with file list and complexity assessment.
```

### Initialize Playwright BDD Project
```
@workspace Set up Playwright BDD project for Browser JS migration:
1. Create playwright.config.ts with defineBddConfig from playwright-bdd
2. Update package.json with @playwright/test and playwright-bdd (NOT @cucumber/cucumber)
3. Create tsconfig.json
4. Create tests/steps/common.steps.ts using createBdd() from playwright-bdd
5. Create a sample .feature file with @Regression tag on all scenarios
```

---

## 📁 Migration Prompts

### Migrate Single Browser JS File
```
@workspace /file:[path-to-file]

Migrate this Browser JS test to Playwright BDD:
1. Convert document.getElementById/querySelector to page.locator()
2. Convert jQuery $() to page.locator()
3. Convert .value assignments to .fill()
4. Convert .click() calls with proper await
5. Replace setTimeout/setInterval with Playwright waiting
6. Create .feature file with @Regression tag on ALL scenarios
7. Create step definitions using createBdd() from playwright-bdd (NOT @cucumber/cucumber)
```

### Convert DOM Selectors
```
Convert all Browser JS DOM selectors in this file to Playwright:
- document.getElementById('x') → page.locator('#x')
- document.querySelector('.x') → page.locator('.x')  
- document.querySelectorAll('.x') → page.locator('.x')
- document.getElementsByClassName('x') → page.locator('.x')
- document.getElementsByName('x') → page.locator('[name="x"]')
- $('.selector') → page.locator('.selector')
- jQuery('.selector') → page.locator('.selector')

Ensure all locators use await where needed.
```

### Convert jQuery to Playwright
```
Convert all jQuery code in this file to Playwright:
- $('.sel').val() → await page.locator('.sel').inputValue()
- $('.sel').val('x') → await page.locator('.sel').fill('x')
- $('.sel').text() → await page.locator('.sel').textContent()
- $('.sel').html() → await page.locator('.sel').innerHTML()
- $('.sel').click() → await page.locator('.sel').click()
- $('.sel').show() → await page.locator('.sel').evaluate(el => el.style.display = 'block')
- $('.sel').hide() → await page.locator('.sel').evaluate(el => el.style.display = 'none')
- $('.sel').attr('x') → await page.locator('.sel').getAttribute('x')
```

### Convert Waits to Playwright
```
Find all setTimeout, setInterval, sleep, and polling loops in this file.
Replace them with proper Playwright waiting:

- setTimeout(() => check(), 1000) → await expect(locator).toBeVisible()
- setInterval with condition → await expect(locator).toPass()
- sleep(2000) → await expect(locator).toHaveText('expected')

Never use waitForTimeout() unless absolutely necessary.
```

### Convert Navigation
```
Convert all Browser JS navigation to Playwright:
- window.location = 'url' → await page.goto('url')
- window.location.href = 'url' → await page.goto('url')
- window.location.reload() → await page.reload()
- history.back() → await page.goBack()

For reading URL:
- window.location.href → page.url()
```

### Convert Storage Operations
```
Convert all localStorage/sessionStorage operations to Playwright:

- localStorage.setItem('k', 'v') → await page.evaluate(() => localStorage.setItem('k', 'v'))
- localStorage.getItem('k') → await page.evaluate(() => localStorage.getItem('k'))
- sessionStorage.setItem('k', 'v') → await page.evaluate(() => sessionStorage.setItem('k', 'v'))
```

---

## 📝 Feature File Prompts

### Generate Feature File from Browser JS Test
```
Analyze this Browser JS test and generate a Gherkin feature file:
1. Extract the test purpose and create Feature description
2. Identify setup steps for Background
3. Convert test actions to When steps
4. Convert assertions to Then steps
5. Add @Regression tag to ALL scenarios (this is mandatory)
6. Use Scenario Outline for data-driven tests
7. Write steps from user perspective
```

### Add Missing @Regression Tags
```
Review this feature file and ensure ALL scenarios have @Regression tag:
- If no tag exists, add @Regression
- If other tags exist but not @Regression, add @Regression
- Feature level should also have @Regression tag

Example:
@Regression
Feature: Login

  @Regression @Smoke
  Scenario: Valid login
    ...

  @Regression
  Scenario: Invalid login
    ...
```

### Create Feature from Browser JS Function
```
Convert this Browser JS function to a Gherkin feature:

Function: [paste function]

Generate:
1. Feature with As a/I want/So that
2. Scenario with Given/When/Then
3. @Regression tag (MANDATORY)
4. Parameterize hardcoded values
```

---

## 🔧 Step Definition Prompts

### Generate Step Definitions (playwright-bdd)
```
Generate step definitions for this feature file using playwright-bdd.

IMPORTANT: Use this exact pattern (NOT @cucumber/cucumber):

import { createBdd } from 'playwright-bdd';
import { expect } from '@playwright/test';

const { Given, When, Then } = createBdd();

Given('step text', async ({ page }) => {
  // implementation
});

- Use page.locator() for all element access
- Use web-first assertions (expect(locator).toBeVisible())
- Add proper TypeScript types
```

### Create Common Steps Library
```
Create a comprehensive common steps file using playwright-bdd createBdd():

Include steps for:
- Navigation (goto, reload, back, forward)
- Clicking elements
- Filling inputs
- Clearing inputs
- Selecting dropdowns
- Checking/unchecking checkboxes
- Visibility assertions
- Text assertions
- Value assertions
- URL assertions

Use: import { createBdd } from 'playwright-bdd';
NOT: import { Given, When, Then } from '@cucumber/cucumber';
```

---

## 🐛 Error Fixing Prompts

### Fix Runtime Errors in Headless Mode
```
This test is failing in headless mode with error: [paste error]

Analyze and fix:
1. Identify the root cause
2. Apply the appropriate fix from these common solutions:
   - strict mode violation → use .first() or more specific selector
   - element not visible → add scrollIntoViewIfNeeded() and visibility wait
   - element intercepted → close overlay or use force: true
   - timeout → increase timeout or add explicit wait
   - element detached → re-query element after DOM change
3. Ensure fix works in both headless and headed mode
```

### Fix Element Not Found Error
```
Fix this "element not found" or "strict mode violation" error:

Error: [paste error]

Apply these fixes:
1. Make selector more specific
2. Add .first() or .nth(n) for multiple matches
3. Wait for element: await expect(locator).toBeVisible()
4. Check if element is in iframe: page.frameLocator('#frame')
5. Verify selector syntax is correct
```

### Fix Timeout Error
```
Fix this timeout error:

Error: [paste error]

Apply these fixes:
1. Increase specific timeout: { timeout: 60000 }
2. Add explicit wait: await expect(locator).toBeVisible()
3. Wait for page load: await page.waitForLoadState('networkidle')
4. Check if element actually exists
```

### Fix Element Intercepted Error
```
Fix this "element intercepted" error:

Error: [paste error]

Apply these fixes:
1. Close any overlays/modals first
2. Scroll element into view: await locator.scrollIntoViewIfNeeded()
3. Wait for animations: add animation disable CSS
4. Use force: true as last resort: await locator.click({ force: true })
```

### Fix Flaky Test
```
This test is flaky (sometimes passes, sometimes fails):

Analyze and stabilize:
1. Replace hardcoded waits with assertions
2. Add proper element visibility waits
3. Handle race conditions with expect().toBeVisible()
4. Ensure test isolation
5. Disable animations if needed
```

### Fix Headless-Specific Issues
```
This test passes in headed mode but fails in headless:

Debug and fix by:
1. Set viewport: viewport: { width: 1920, height: 1080 }
2. Add explicit waits for dynamic content
3. Disable animations with CSS injection
4. Check for hover-dependent elements
5. Add userAgent if site detects headless
```

---

## 📦 Configuration Prompts

### Setup Playwright Config for BDD
```
Create playwright.config.ts for Playwright BDD:

Use defineBddConfig from playwright-bdd (NOT cucumberReporter):

import { defineConfig, devices } from '@playwright/test';
import { defineBddConfig } from 'playwright-bdd';

const testDir = defineBddConfig({
  features: 'tests/features/**/*.feature',
  steps: 'tests/steps/**/*.ts',
});

Include:
- Headless mode with proper viewport
- Screenshot/video on failure
- Trace on retry
- Reasonable timeouts
```

### Update Package.json
```
Update package.json for Playwright BDD:

Add dependencies:
- @playwright/test
- playwright-bdd

DO NOT add @cucumber/cucumber

Add scripts:
- "test": "npx bddgen && playwright test"
- "test:headed": "npx bddgen && playwright test --headed"
- "test:debug": "npx bddgen && playwright test --debug"
- "test:ui": "npx bddgen && playwright test --ui"
```

---

## 🔄 Batch Operations

### Migrate All Browser JS Files
```
@workspace Migrate all Browser JS test files:

1. Find all files with document.*, $(), jQuery patterns
2. For each file:
   - Create .feature file with @Regression tag on ALL scenarios
   - Create step definitions using createBdd() from playwright-bdd
   - Convert all Browser JS patterns to Playwright
3. Generate common.steps.ts with shared steps
4. Ensure NO @cucumber/cucumber imports exist
```

### Fix All Failing Tests
```
@workspace Run all tests in headless mode and fix failures:

1. Run: npx bddgen && playwright test
2. For each failure:
   - Identify error type (timeout, not visible, intercepted, etc.)
   - Apply appropriate fix
   - Re-run to verify
3. Ensure all tests pass in headless mode
```

### Verify No Cucumber References
```
@workspace Scan entire project and remove any @cucumber/cucumber references:

1. Search for: import { Given, When, Then } from '@cucumber/cucumber'
2. Replace with: import { createBdd } from 'playwright-bdd'
3. Update step definitions to use: const { Given, When, Then } = createBdd();
4. Remove @cucumber/cucumber from package.json if present
```

### Add @Regression Tags to All Features
```
@workspace Scan all .feature files and ensure @Regression tag:

1. If Feature has no tag, add @Regression
2. If Scenario has no tag, add @Regression
3. Keep existing tags, just ensure @Regression is present
4. Report which files were modified
```

---

## Usage Tips

1. **Always @Regression**: Every feature and scenario needs `@Regression` tag
2. **Use playwright-bdd**: Use `createBdd()`, never `@cucumber/cucumber`
3. **Test headless**: Always verify tests pass in headless mode
4. **No setTimeout**: Replace all waits with Playwright assertions
5. **Fix errors**: Apply specific fixes for each error type

## VS Code Keyboard Shortcuts

- `Ctrl+Shift+I` / `Cmd+Shift+I` - Open Copilot Chat
- `Ctrl+I` / `Cmd+I` - Inline suggestions
- `/fix` - Quick fix selected code
- `/explain` - Explain selected code
