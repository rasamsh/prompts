# VS Code Setup Guide for Playwright BDD Migration Agent

## Prerequisites

1. **VS Code** installed
2. **GitHub Copilot** extension installed and activated
3. **Node.js 18+** installed

---

## Step 1: Install Required VS Code Extensions

Open VS Code and install these extensions:

1. **GitHub Copilot** (required)
   - Press `Ctrl+Shift+X` (Extensions)
   - Search: "GitHub Copilot"
   - Install

2. **GitHub Copilot Chat** (required)
   - Search: "GitHub Copilot Chat"
   - Install

3. **Playwright Test for VS Code** (recommended)
   - Search: "Playwright Test for VSCode"
   - Install

4. **Cucumber (Gherkin) Full Support** (recommended)
   - Search: "Cucumber (Gherkin) Full Support"
   - Install

---

## Step 2: Configure Copilot Custom Instructions

### Option A: Project-Level Instructions (Recommended)

1. In your project root, create `.github` folder:
   ```
   mkdir -p .github
   ```

2. Copy the `copilot-instructions.md` file:
   ```
   cp docs/copilot-instructions.md .github/copilot-instructions.md
   ```

3. VS Code will automatically use these instructions for Copilot in this project.

### Option B: VS Code Settings

1. Open VS Code Settings (`Ctrl+,`)
2. Search for "Copilot"
3. Find "GitHub Copilot: Chat: Code Generation Instructions"
4. Click "Edit in settings.json"
5. Add:
   ```json
   {
     "github.copilot.chat.codeGeneration.instructions": [
       {
         "file": ".github/copilot-instructions.md"
       }
     ]
   }
   ```

---

## Step 3: Open Your Project

1. Open VS Code
2. File → Open Folder → Select your test project
3. Ensure the project has the migration agent files

---

## Step 4: Using Copilot Chat for Migration

### Open Copilot Chat

- **Keyboard**: `Ctrl+Shift+I` (Windows/Linux) or `Cmd+Shift+I` (Mac)
- **Or**: Click the Copilot Chat icon in the sidebar

### Basic Migration Workflow

**1. Analyze your project:**
```
@workspace Analyze all test files and identify Browser JS patterns like document.getElementById, querySelector, $() jQuery selectors. Create a migration report.
```

**2. Migrate a single file:**
```
@workspace /file:tests/login.test.js

Migrate this Browser JS test to Playwright BDD:
- Use createBdd() from playwright-bdd (not @cucumber/cucumber)
- Add @Regression tag to all scenarios
- Convert all document.getElementById to page.locator()
```

**3. Generate step definitions:**
```
@workspace Generate step definitions for the feature files using playwright-bdd createBdd(). Do not use @cucumber/cucumber.
```

**4. Fix failing tests:**
```
@workspace This test fails in headless mode with error: [paste error]
Fix it following the runtime error fixing guidelines.
```

---

## Step 5: Running Tests

### In Terminal (inside VS Code)

```bash
# Install dependencies
npm install

# Install Playwright browsers
npx playwright install

# Generate BDD tests and run
npm test
# This runs: npx bddgen && playwright test

# Run in headed mode (see browser)
npm run test:headed

# Run with UI mode (interactive debugging)
npm run test:ui

# Run in debug mode
npm run test:debug
```

### Using Playwright VS Code Extension

1. Click the "Testing" icon in the sidebar (flask icon)
2. You'll see all your tests listed
3. Click the play button to run individual tests
4. Right-click for more options (debug, run headed, etc.)

---

## Step 6: Using the Prompts

Open Copilot Chat (`Ctrl+Shift+I`) and use prompts from `docs/PROMPTS.md`:

### Quick Start Prompts

**Analyze tests:**
```
@workspace Analyze all test files and identify Browser JS patterns
```

**Setup project:**
```
@workspace Set up Playwright BDD project with playwright-bdd (not cucumber)
```

### Migration Prompts

**Migrate file:**
```
@workspace /file:[path-to-file]
Migrate this Browser JS test to Playwright BDD with @Regression tags
```

**Convert jQuery:**
```
Convert all jQuery $() selectors in this file to Playwright page.locator()
```

### Error Fixing Prompts

**Fix timeout:**
```
This test has a timeout error. Add proper visibility waits.
```

**Fix multiple elements:**
```
This test fails with "strict mode violation". Make selectors more specific.
```

---

## Step 7: Keyboard Shortcuts Reference

| Action | Windows/Linux | Mac |
|--------|---------------|-----|
| Open Copilot Chat | `Ctrl+Shift+I` | `Cmd+Shift+I` |
| Inline Copilot | `Ctrl+I` | `Cmd+I` |
| Accept suggestion | `Tab` | `Tab` |
| Reject suggestion | `Esc` | `Esc` |
| Open command palette | `Ctrl+Shift+P` | `Cmd+Shift+P` |
| Open terminal | `Ctrl+`` | `Cmd+`` |
| Run tests | `Ctrl+Shift+P` → "Test: Run All" | `Cmd+Shift+P` → "Test: Run All" |

---

## Step 8: Troubleshooting

### Copilot not using custom instructions
- Ensure `.github/copilot-instructions.md` exists in project root
- Restart VS Code
- Check Copilot is signed in and active

### Tests failing in headless mode
- Use prompts from PROMPTS.md to fix errors
- Common fixes:
  - Add `.first()` for multiple elements
  - Add `scrollIntoViewIfNeeded()`
  - Increase timeouts
  - Disable animations

### BDD tests not generating
- Run `npx bddgen` manually first
- Check feature files are in correct location
- Verify step definitions match step text

### Playwright extension not showing tests
- Ensure `playwright.config.ts` exists
- Run `npx playwright install`
- Reload VS Code window (`Ctrl+Shift+P` → "Reload Window")

---

## Project Structure

After setup, your project should look like:

```
your-project/
├── .github/
│   └── copilot-instructions.md    ← Copilot uses this
├── tests/
│   ├── features/
│   │   └── *.feature              ← Gherkin files with @Regression
│   └── steps/
│       └── *.steps.ts             ← Step definitions (playwright-bdd)
├── playwright.config.ts
├── package.json
└── tsconfig.json
```

---

## Quick Reference Card

### Migration Pattern
```
Browser JS                      Playwright
─────────────────────────────────────────────────
document.getElementById('x')  → page.locator('#x')
document.querySelector('.x')  → page.locator('.x')
$('.selector')                → page.locator('.selector')
element.value = 'x'           → await locator.fill('x')
element.click()               → await locator.click()
setTimeout(fn, ms)            → await expect(locator).toBeVisible()
window.location = 'url'       → await page.goto('url')
```

### Step Definition Pattern
```typescript
// CORRECT - use playwright-bdd
import { createBdd } from 'playwright-bdd';
const { Given, When, Then } = createBdd();

Given('step', async ({ page }) => { ... });

// WRONG - do not use cucumber
import { Given } from '@cucumber/cucumber'; // ❌ NO
```

### Feature File Pattern
```gherkin
@Regression                    ← Always add this
Feature: Login

  @Regression                  ← Always add this
  Scenario: User logs in
    Given I am on the application
    When I click on "#login"
    Then I should see "Welcome"
```
