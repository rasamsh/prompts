# VS Code Copilot Agent Setup Guide

Complete guide to set up and run the Browser JS → Playwright BDD migration agent in VS Code.

## What This Agent Does

1. **Converts Browser JS to Playwright BDD** - `document.*`, `$()`, jQuery → Playwright
2. **Uses playwright-bdd** - NOT `@cucumber/cucumber`
3. **JavaScript by default** - TypeScript is optional
4. **Adds @Regression tags** - All scenarios get `@Regression` tag
5. **Fixes runtime errors** - Detects and fixes headless browser issues
6. **Improves variable names** - Renames `e`, `btn`, `val` to descriptive names

---

## Prerequisites

- VS Code 1.85 or later
- GitHub Copilot subscription (Individual, Business, or Enterprise)
- Node.js 18+

---

## Step 1: Install Required Extensions

Open VS Code and press `Ctrl+Shift+X` to open Extensions. Install:

| Extension | Required | Purpose |
|-----------|----------|---------|
| GitHub Copilot | ✅ Yes | AI code completion |
| GitHub Copilot Chat | ✅ Yes | Chat interface for prompts |
| Playwright Test for VSCode | Recommended | Run/debug tests |
| Cucumber (Gherkin) Full Support | Recommended | Feature file syntax |

**Install via command line:**
```bash
code --install-extension GitHub.copilot
code --install-extension GitHub.copilot-chat
code --install-extension ms-playwright.playwright
code --install-extension alexkrechik.cucumberautocomplete
```

---

## Step 2: Sign in to GitHub Copilot

1. Click the **Copilot icon** in the bottom-right status bar
2. Click **"Sign in to GitHub"**
3. Complete authentication in browser
4. Return to VS Code - you should see "Copilot" active in status bar

---

## Step 3: Set Up Custom Agent Instructions

### Method A: Project-Level (Recommended)

This makes the agent available for your specific project.

**1. Create the `.github` folder in your project root:**

```bash
cd your-project
mkdir -p .github
```

**2. Copy the instructions file:**

```bash
# If you have the migration agent files:
cp path/to/playwright-migration-agent/docs/copilot-instructions.md .github/copilot-instructions.md
```

**3. Verify the file exists:**
```
your-project/
├── .github/
│   └── copilot-instructions.md    ← Agent instructions
├── tests/
├── package.json
└── ...
```

### Method B: User-Level Settings (All Projects)

**1. Open VS Code Settings:**
- Press `Ctrl+,` (Windows/Linux) or `Cmd+,` (Mac)

**2. Search for "copilot instructions"**

**3. Click "Edit in settings.json"**

**4. Add this configuration:**

```json
{
  "github.copilot.chat.codeGeneration.instructions": [
    {
      "file": "${workspaceFolder}/.github/copilot-instructions.md"
    }
  ]
}
```

---

## Step 4: Agent Rules Summary

The agent follows these rules (from `copilot-instructions.md`):

### Rule 1: Browser JS Only
Only converts native Browser JavaScript patterns:
- `document.getElementById()` → `page.locator('#id')`
- `document.querySelector()` → `page.locator()`
- `$()` jQuery → `page.locator()`
- `.value`, `.innerText`, `.innerHTML` → Playwright methods
- `.click()`, `.focus()`, `.submit()` → Playwright actions
- `window.location` → `page.goto()`
- `localStorage`, `sessionStorage` → `page.evaluate()`

### Rule 2: Use playwright-bdd
- Import from `playwright-bdd`, NOT `@cucumber/cucumber`
- Use `createBdd()` pattern:

```javascript
const { createBdd } = require('playwright-bdd');
const { Given, When, Then } = createBdd();
```

### Rule 3: JavaScript by Default
- Generate `.js` files unless TypeScript requested
- Use `require()` syntax, NOT `import`
- Use `module.exports`, NOT `export`

### Rule 4: Default @Regression Tag
- ALL scenarios must have `@Regression` tag
- Add to Feature level and each Scenario

```gherkin
@Regression
Feature: Login

  @Regression
  Scenario: Valid login
    Given I am on the application
    When I click on "#login"
    Then I should see "Welcome"
```

### Rule 5: Fix Runtime Errors
Automatically fix headless browser issues:

| Error | Fix |
|-------|-----|
| `strict mode violation` | Add `.first()` or specific selector |
| `element not visible` | Add `scrollIntoViewIfNeeded()` |
| `element intercepted` | Close overlay or use `force: true` |
| `timeout exceeded` | Increase timeout, add visibility wait |
| `element detached` | Re-query element after DOM change |

### Rule 6: No Hardcoded Waits
Replace `setTimeout`/`setInterval` with proper Playwright waiting:

```javascript
// BAD
await page.waitForTimeout(2000);

// GOOD
await expect(page.locator('.result')).toBeVisible();
```

### Rule 7: Improve Variable Names
Rename poorly named variables to be descriptive:

| Bad Name | Good Name (by context) |
|----------|----------------------|
| `e`, `el`, `elem` | `loginButton`, `emailInput`, `modalDialog` |
| `btn` | `submitButton`, `cancelButton`, `nextButton` |
| `val`, `v` | `inputValue`, `selectedOption`, `currentPrice` |
| `str`, `s` | `errorMessage`, `userName`, `pageTitle` |
| `arr`, `list` | `userList`, `menuItems`, `searchResults` |
| `obj`, `data` | `userData`, `formData`, `apiResponse` |
| `flag`, `bool` | `isLoggedIn`, `hasError`, `isVisible` |
| `res`, `resp` | `serverResponse`, `validationResult` |
| `temp`, `tmp` | `previousValue`, `cachedData` |
| `i`, `j`, `n` | `rowIndex`, `itemCount`, `pageNumber` |

**Naming Conventions:**
- camelCase for all variables
- Prefix booleans with `is`, `has`, `can`, `should`
- Pluralize arrays (`users` not `user`)
- No single-letter names
- No abbreviations (`button` not `btn`)

---

## Step 5: Open Copilot Chat

### Keyboard Shortcuts

| Action | Windows/Linux | Mac |
|--------|---------------|-----|
| Open Copilot Chat Panel | `Ctrl+Shift+I` | `Cmd+Shift+I` |
| Inline Chat | `Ctrl+I` | `Cmd+I` |
| Quick Chat | `Ctrl+Shift+Alt+L` | `Cmd+Shift+Alt+L` |

### Via UI

1. Click the **Chat icon** in the left sidebar (speech bubble)
2. Or click **Copilot icon** in bottom status bar → "Open Chat"

---

## Step 6: Using the Agent

### Migration Workflow

**1. Analyze your project:**
```
@workspace Analyze all test files and identify:
- Browser JS patterns (document.*, $(), jQuery)
- setTimeout/setInterval usage
- Poorly named variables (e, btn, val, arr, etc.)
Create a migration report.
```

**2. Migrate a single file:**
```
@workspace /file:tests/login.test.js

Migrate this Browser JS test to Playwright BDD:
- Convert selectors to page.locator()
- Create .feature file with @Regression tags
- Create .js step definitions using createBdd()
- Rename all poorly named variables
```

**3. Fix variable names only:**
```
@workspace /file:tests/steps/common.steps.js

Rename all poorly named variables:
- e, el → descriptive element names
- btn → submitButton, cancelButton, etc.
- val → inputValue, selectedValue, etc.
- arr → itemList, userArray, etc.
```

**4. Fix runtime errors:**
```
This test fails with "strict mode violation". 
Fix by making selectors more specific and use descriptive variable names.
```

**5. Verify migration:**
```
@workspace Verify all code:
- Features have @Regression tag
- Steps use createBdd() from playwright-bdd
- No @cucumber/cucumber imports
- All variables have descriptive names
```

### Common Prompts

**Analyze:**
```
@workspace Analyze for Browser JS patterns and poorly named variables
```

**Migrate file:**
```
@workspace /file:path/to/file.js
Migrate to Playwright BDD with @Regression tags and good variable names
```

**Fix names only:**
```
@workspace /file:path/to/file.js
Rename all poorly named variables (e, btn, val, arr) to descriptive names
```

**Fix errors:**
```
Fix this timeout error and rename variables to be descriptive
```

**Generate steps:**
```
Generate step definitions using createBdd() with descriptive variable names
```

---

## Step 7: Running Tests

### In VS Code Terminal

Press `` Ctrl+` `` to open terminal:

```bash
# Install dependencies
npm install

# Install browsers
npx playwright install

# Generate and run tests
npm test

# Run with visible browser
npm run test:headed

# Run with UI mode
npm run test:ui

# Debug mode
npm run test:debug
```

### Using Playwright Extension

1. Click **Testing icon** in sidebar (flask icon)
2. Click **Refresh** to discover tests
3. Click **Play button** next to test to run
4. Right-click for: Run, Debug, Run Headed

---

## Step 8: Project Structure

After setup, your project should look like:

```
your-project/
├── .github/
│   └── copilot-instructions.md    ← Agent instructions
├── .vscode/
│   └── settings.json              ← VS Code settings (optional)
├── tests/
│   ├── features/
│   │   └── *.feature              ← @Regression tagged
│   └── steps/
│       └── *.steps.js             ← createBdd() pattern, good names
├── playwright.config.js
├── package.json
└── node_modules/
```

---

## Step 9: Troubleshooting

### Agent Not Using Custom Instructions

**Check 1:** Verify file location
```
your-project/
└── .github/
    └── copilot-instructions.md   ← Must be here
```

**Check 2:** Restart VS Code
- Press `Ctrl+Shift+P` → "Reload Window"

**Check 3:** Verify Copilot is active
- Check status bar shows "Copilot"

### Variable Names Not Being Improved

Explicitly ask in your prompt:
```
Also rename all poorly named variables:
- e, el → descriptive element name
- btn → buttonName based on purpose
- val → valueName based on content
```

### Tests Failing in Headless Mode

Use the error fixing prompts:
```
This test fails in headless with error: [paste error]
Fix it and use descriptive variable names.
```

### Still Using @cucumber/cucumber

Explicitly state:
```
Use createBdd() from playwright-bdd, NOT @cucumber/cucumber
```

---

## Quick Reference Card

### All Agent Rules

| Rule | Description |
|------|-------------|
| 1 | Browser JS only (document.*, $(), jQuery) |
| 2 | Use playwright-bdd, NOT @cucumber/cucumber |
| 3 | JavaScript by default (.js files, require() syntax) |
| 4 | Add @Regression tag to all scenarios |
| 5 | Fix runtime errors (headless issues) |
| 6 | No hardcoded waits (use Playwright waiting) |
| 7 | Improve variable names (descriptive, contextual) |

### Variable Naming Quick Reference

```javascript
// BAD → GOOD
const e = page.locator('#email');           // → emailInput
const b = page.locator('.btn');             // → submitButton
const v = await input.inputValue();         // → currentValue
const arr = await items.all();              // → allMenuItems
const flag = await el.isVisible();          // → isElementVisible
const data = await response.json();         // → userData
```

### Keyboard Shortcuts

| Action | Windows/Linux | Mac |
|--------|---------------|-----|
| Open Copilot Chat | `Ctrl+Shift+I` | `Cmd+Shift+I` |
| Inline Chat | `Ctrl+I` | `Cmd+I` |
| Open Terminal | `` Ctrl+` `` | `` Cmd+` `` |
| Command Palette | `Ctrl+Shift+P` | `Cmd+Shift+P` |
| Settings | `Ctrl+,` | `Cmd+,` |

### Step Definition Pattern

```javascript
// tests/steps/common.steps.js
const { createBdd } = require('playwright-bdd');
const { expect } = require('@playwright/test');

const { Given, When, Then } = createBdd();

When('I click on {string}', async ({ page }, selector) => {
  const targetElement = page.locator(selector);  // Good name!
  await targetElement.click();
});

Then('I should see {string}', async ({ page }, expectedText) => {
  const textElement = page.getByText(expectedText);  // Good name!
  await expect(textElement).toBeVisible();
});
```

### Feature File Pattern

```gherkin
@Regression
Feature: Login

  @Regression
  Scenario: User logs in successfully
    Given I am on the application
    When I enter "user@test.com" in "#email"
    And I enter "password123" in "#password"
    And I click on ".login-btn"
    Then the URL should contain "/dashboard"
```

---

## Additional Resources

- [GitHub Copilot Docs](https://docs.github.com/copilot)
- [Playwright BDD Docs](https://vitalets.github.io/playwright-bdd/)
- [Playwright Docs](https://playwright.dev)
