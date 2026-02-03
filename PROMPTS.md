# Browser JS → Playwright BDD Migration Prompts

VS Code Copilot prompts for migrating **Browser JavaScript** to **Playwright BDD**.

**Important**: 
- Uses `playwright-bdd` package, NOT `@cucumber/cucumber`
- Generates **JavaScript** by default (TypeScript is optional)
- **Improves variable names** to be descriptive and contextual

---

## 🚀 Quick Start

### Analyze Browser JS Tests
```
@workspace Analyze all test files and identify:
1. Browser JS patterns (document.getElementById, querySelector, $() jQuery)
2. DOM property access (.value, .innerText, .innerHTML)
3. DOM actions (.click(), .focus(), .submit())
4. setTimeout/setInterval usage
5. Poorly named variables (single letters, abbreviations, non-descriptive names)

Create a migration report with file list, complexity assessment, and variables that need renaming.
```

### Initialize Playwright BDD Project (JavaScript)
```
@workspace Set up Playwright BDD project using JavaScript:
1. Create playwright.config.js with defineBddConfig from playwright-bdd
2. Update package.json with @playwright/test and playwright-bdd
3. Create tests/steps/common.steps.js using createBdd() from playwright-bdd
4. Create a sample .feature file with @Regression tag on all scenarios
5. Use require() syntax, NOT import statements
6. Use descriptive variable names throughout (no single-letter names)
```

---

## 📁 Migration Prompts (JavaScript)

### Migrate Single Browser JS File
```
@workspace /file:[path-to-file]

Migrate this Browser JS test to Playwright BDD using JavaScript:
1. Convert document.getElementById/querySelector to page.locator()
2. Convert jQuery $() to page.locator()
3. Convert .value assignments to .fill()
4. Convert .click() calls with proper await
5. Replace setTimeout/setInterval with Playwright waiting
6. Create .feature file with @Regression tag on ALL scenarios
7. Create .js step definitions using createBdd() from playwright-bdd
8. Use require() syntax, NOT import/export
9. RENAME all poorly named variables:
   - e, el, elem → descriptive element name (loginButton, emailInput)
   - x, y, z, a, b, c → meaningful names based on context
   - btn → submitButton, cancelButton, etc.
   - val, v → inputValue, selectedValue, etc.
   - arr, list → itemList, userArray, etc.
   - str, s → errorMessage, userName, etc.
```

### Convert and Rename Variables
```
@workspace /file:[path-to-file]

Analyze this file and improve ALL variable names:

1. Find poorly named variables:
   - Single letters (e, x, i, n, s, v, etc.)
   - Abbreviations (btn, val, elem, arr, obj, etc.)
   - Generic names (data, temp, result, item, etc.)

2. Rename based on context:
   - Form elements: emailInput, passwordField, submitButton
   - User data: userName, userEmail, userProfile
   - Lists: menuItems, searchResults, userList
   - Booleans: isLoggedIn, hasError, isVisible
   - Counts: itemCount, retryAttempts, pageNumber

3. Apply naming conventions:
   - camelCase for variables
   - Prefix booleans with is/has/can/should
   - Pluralize arrays
   - Be specific, not generic

Show before/after for each renamed variable.
```

### Fix Variable Names Only
```
@workspace /file:[path-to-file]

Review this file and rename ALL poorly named variables without changing functionality:

BAD → GOOD examples:
- e → targetElement, clickedButton, inputField
- el → loginForm, navigationMenu, modalDialog
- btn → submitButton, closeButton, nextPageButton
- val → currentValue, selectedOption, inputText
- arr → userList, menuItems, searchResults
- str → errorMessage, welcomeText, pageTitle
- obj → userData, formData, configOptions
- data → apiResponse, userProfile, searchResults
- temp → previousValue, cachedResult
- flag → isValid, hasLoaded, canSubmit
- res → serverResponse, validationResult
- i, j → rowIndex, columnIndex, itemIndex

Provide the complete refactored code with all variables renamed.
```

### Convert DOM Selectors with Good Names
```
Convert all Browser JS DOM selectors in this file to Playwright.
Use descriptive variable names based on what the element represents:

BEFORE:
const e = document.getElementById('email');
const p = document.getElementById('pass');
const b = document.querySelector('.btn');

AFTER:
const emailInput = page.locator('#email');
const passwordInput = page.locator('#pass');
const submitButton = page.locator('.btn');

Apply this pattern to ALL selectors in the file.
Generate JavaScript code using require() syntax.
```

### Convert jQuery to Playwright with Good Names
```
Convert all jQuery code in this file to Playwright using JavaScript.
Rename all variables to be descriptive:

BEFORE:
const v = $('.input').val();
const t = $('.title').text();
$('.btn').click();

AFTER:
const inputValue = await page.locator('.input').inputValue();
const titleText = await page.locator('.title').textContent();
const submitButton = page.locator('.btn');
await submitButton.click();

Use require() syntax for imports.
```

### Convert Waits to Playwright
```
Find all setTimeout, setInterval, sleep, and polling loops in this file.
Replace them with proper Playwright waiting and use good variable names:

BEFORE:
setTimeout(() => {
  const e = document.querySelector('.result');
  if (e) console.log(e.innerText);
}, 1000);

AFTER:
const resultElement = page.locator('.result');
await expect(resultElement).toBeVisible();
const resultText = await resultElement.textContent();
console.log(resultText);

Never use waitForTimeout() unless absolutely necessary.
Generate JavaScript code with descriptive names.
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
8. Use descriptive step text (not generic)
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

---

## 🔧 Step Definition Prompts (JavaScript)

### Generate Step Definitions with Good Variable Names
```
Generate JavaScript step definitions for this feature file using playwright-bdd.

Use descriptive variable names:

const { createBdd } = require('playwright-bdd');
const { expect } = require('@playwright/test');

const { Given, When, Then } = createBdd();

// GOOD - descriptive parameter and variable names
When('I enter {string} in the email field', async ({ page }, emailAddress) => {
  const emailInput = page.locator('#email');
  await emailInput.fill(emailAddress);
});

// BAD - avoid this
When('I enter {string} in the email field', async ({ page }, s) => {
  const e = page.locator('#email');
  await e.fill(s);
});

Use require() syntax, NOT import.
Generate .js files, NOT .ts.
```

### Create Common Steps Library (JavaScript)
```
Create a comprehensive common steps file using JavaScript and playwright-bdd createBdd().

Requirements:
1. Use descriptive variable names throughout:
   - targetElement, inputField, submitButton (not e, el, btn)
   - expectedText, inputValue, optionValue (not s, v, val)
   - targetUrl, pageTitle, errorMessage (not url, t, msg)

2. Include steps for:
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

Use: const { createBdd } = require('playwright-bdd');
NOT: import { Given, When, Then } from '@cucumber/cucumber';
Generate .js file with require() syntax.
```

---

## 🐛 Error Fixing Prompts

### Fix Runtime Errors with Good Variable Names
```
This test is failing in headless mode with error: [paste error]

Analyze and fix using JavaScript:
1. Identify the root cause
2. Apply the appropriate fix
3. ALSO rename any poorly named variables found in the code:
   - el, e, elem → descriptive name based on element type
   - btn → submitButton, closeButton, etc.
   - val, v → inputValue, currentValue, etc.
4. Ensure fix works in both headless and headed mode
5. Generate JavaScript code with good variable names
```

### Fix Element Not Found Error
```
Fix this "element not found" or "strict mode violation" error:

Error: [paste error]

Apply these fixes (generate JavaScript with good variable names):
1. Make selector more specific
2. Add .first() or .nth(n) for multiple matches
3. Wait for element: await expect(locator).toBeVisible()
4. Use descriptive variable names:
   - const submitButton = page.locator('.btn').first();
   - NOT: const b = page.locator('.btn').first();
```

### Fix Timeout Error
```
Fix this timeout error:

Error: [paste error]

Apply these fixes (generate JavaScript with good variable names):
1. Increase specific timeout: { timeout: 60000 }
2. Add explicit wait: await expect(locator).toBeVisible()
3. Use descriptive names:
   - const slowLoadingElement = page.locator('#slow');
   - await expect(slowLoadingElement).toBeVisible({ timeout: 60000 });
```

### Fix Flaky Test
```
This test is flaky (sometimes passes, sometimes fails):

Analyze and stabilize using JavaScript:
1. Replace hardcoded waits with assertions
2. Add proper element visibility waits
3. Handle race conditions with expect().toBeVisible()
4. Ensure test isolation
5. Rename any poorly named variables to be descriptive
6. Disable animations if needed
```

---

## 📦 Configuration Prompts

### Setup Playwright Config (JavaScript)
```
Create playwright.config.js for Playwright BDD.
Use descriptive variable names:

const { defineConfig, devices } = require('@playwright/test');
const { defineBddConfig } = require('playwright-bdd');

const testDirectory = defineBddConfig({
  features: 'tests/features/**/*.feature',
  steps: 'tests/steps/**/*.js',
});

module.exports = defineConfig({
  testDir: testDirectory,
  // ... rest of config with descriptive names
});

Include:
- Headless mode with proper viewport
- Screenshot/video on failure
- Trace on retry
- Reasonable timeouts
```

### Update Package.json (JavaScript)
```
Update package.json for Playwright BDD with JavaScript:

Add dependencies:
- @playwright/test
- playwright-bdd

DO NOT add:
- @cucumber/cucumber
- typescript (unless specifically requested)

Add scripts:
- "test": "npx bddgen && playwright test"
- "test:headed": "npx bddgen && playwright test --headed"
- "test:debug": "npx bddgen && playwright test --debug"
- "test:ui": "npx bddgen && playwright test --ui"
```

---

## 🔄 Batch Operations

### Migrate All Files and Fix Variable Names
```
@workspace Migrate all Browser JS test files using JavaScript:

1. Find all files with document.*, $(), jQuery patterns
2. For each file:
   - Create .feature file with @Regression tag on ALL scenarios
   - Create .js step definitions using createBdd() from playwright-bdd
   - Convert all Browser JS patterns to Playwright
   - RENAME ALL poorly named variables to be descriptive
3. Generate common.steps.js with shared steps (good variable names)
4. Use require() syntax throughout
5. Ensure NO @cucumber/cucumber imports exist

Variable naming rules:
- e, el, elem → elementName based on context
- btn → buttonName (submitButton, closeButton)
- val, v → valueName (inputValue, selectedValue)
- arr → arrayName (itemList, userArray)
- str, s → stringName (errorMessage, userName)
```

### Fix All Variable Names in Project
```
@workspace Scan all .js files in tests/ and rename poorly named variables:

Find and rename:
- Single letter variables (e, x, i, n, s, v, a, b, c)
- Abbreviations (btn, val, elem, arr, obj, str, num)
- Generic names (data, temp, result, item, thing)

Apply context-aware naming:
- Form elements: emailInput, passwordField, submitButton
- API responses: userData, apiResponse, serverResult
- DOM elements: menuContainer, headerSection, footerLinks
- State: isLoggedIn, hasError, isLoading
- Lists: userList, menuItems, searchResults

Generate a report of all changes made.
```

### Verify Code Quality
```
@workspace Verify all migrated code:

1. Check all features have @Regression tag
2. Check no @cucumber/cucumber imports
3. Check all steps use createBdd()
4. Check variable naming:
   - No single-letter names (except standard loop counters)
   - No abbreviations (btn, val, elem)
   - All booleans prefixed with is/has/can/should
   - All arrays use plural names
   - All names are descriptive and contextual

Report any violations found.
```

---

## Variable Naming Quick Reference

### Bad → Good Examples

| Bad | Good (based on context) |
|-----|------------------------|
| `e` | `loginButton`, `emailInput`, `modalDialog` |
| `el` | `navigationMenu`, `formContainer`, `headerSection` |
| `btn` | `submitButton`, `cancelButton`, `nextButton` |
| `val` | `inputValue`, `selectedOption`, `currentPrice` |
| `str` | `errorMessage`, `userName`, `pageTitle` |
| `arr` | `userList`, `menuItems`, `searchResults` |
| `obj` | `userData`, `formData`, `configOptions` |
| `data` | `apiResponse`, `userProfile`, `searchResults` |
| `temp` | `previousValue`, `cachedResult`, `backupData` |
| `flag` | `isValid`, `hasLoaded`, `canSubmit` |
| `res` | `serverResponse`, `validationResult`, `queryResult` |
| `i` | `rowIndex`, `itemIndex`, `pageNumber` |
| `n` | `itemCount`, `retryAttempts`, `maxPages` |
| `x`, `y` | `horizontalPosition`, `verticalOffset` |

### Naming Patterns

```javascript
// Elements - name by purpose
const emailInput = page.locator('#email');
const submitButton = page.locator('.submit-btn');
const errorMessage = page.locator('.error');

// Values - name by content
const currentUsername = await usernameInput.inputValue();
const selectedCountry = await countryDropdown.inputValue();

// Booleans - prefix with is/has/can/should
const isUserLoggedIn = await loginStatus.isVisible();
const hasValidationError = await errorMessage.isVisible();

// Lists - use plurals
const menuItems = page.locator('.menu-item');
const allUsers = await userList.all();

// Responses - name by source
const loginResponse = await page.waitForResponse('/api/login');
const userData = await loginResponse.json();
```

---

## Usage Tips

1. **Always @Regression**: Every feature and scenario needs `@Regression` tag
2. **Use playwright-bdd**: Use `createBdd()`, never `@cucumber/cucumber`
3. **JavaScript by default**: Use `.js` files unless TypeScript specifically requested
4. **require() syntax**: Use `require()` not `import` for JavaScript
5. **Test headless**: Always verify tests pass in headless mode
6. **No setTimeout**: Replace all waits with Playwright assertions
7. **Descriptive names**: Every variable should describe its purpose
8. **No abbreviations**: Write out full words (button not btn, element not el)

## VS Code Keyboard Shortcuts

- `Ctrl+Shift+I` / `Cmd+Shift+I` - Open Copilot Chat
- `Ctrl+I` / `Cmd+I` - Inline suggestions
- `/fix` - Quick fix selected code
- `/explain` - Explain selected code
