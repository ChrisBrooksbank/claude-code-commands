---
name: cb-guard-rails
author: ChrisBrooksbank
description: Add guard rails to existing projects - detect tech stack, identify missing tools, and add linting, formatting, pre-commit hooks, testing, visual regression & interaction testing (Playwright), and CI.
---

This skill retrofits existing projects with "guard rails" - tools that create tight feedback loops to catch errors early. Unlike scaffolding a new project, this detects what's already in place and only adds what's missing.

## Philosophy

| Tool | Catches |
|------|---------|
| Linting | Code patterns, potential bugs, style issues |
| Formatting | Style inconsistencies |
| Pre-commit hooks | Stops bad code before commit |
| Type checking | Type errors, null issues |
| Testing | Behavioral regressions |
| Dead code detection | Unused exports, dependencies, files |
| Visual & interaction testing | Visual regressions, broken UI, non-functional interactivity |
| CI | Issues missed locally |

## When to Use

- Existing project lacks proper tooling
- User asks to "add linting" or "set up pre-commit hooks"
- Onboarding a project that will involve ongoing LLM assistance
- Project has some tools but is missing others

## Key Difference from cb-new-project

| Aspect | cb-new-project | cb-guard-rails |
|--------|---------------|----------------|
| Purpose | Scaffold new projects | Retrofit existing projects |
| Detection | N/A | Detects tech stack and existing tools |
| Duplication | Creates all files | Only adds missing tools |
| Languages | TypeScript only | TS/JS, Python, Go, Rust |

---

## Detection Process

Before adding any guard rails, you MUST complete all detection phases. Use the Read and Glob tools to examine the project.

### Phase 1: Tech Stack Detection

Check for these files to determine the primary language and package manager:

| File | Language | Package Manager |
|------|----------|-----------------|
| `package.json` | JavaScript/TypeScript | npm/yarn/pnpm (check lockfiles) |
| `pyproject.toml` | Python | poetry/uv/pip |
| `requirements.txt` | Python | pip |
| `go.mod` | Go | go modules |
| `Cargo.toml` | Rust | cargo |

**Also read for context** (if they exist):
- `TECH_STACK.md` - Explicit tech stack documentation
- `CLAUDE.md` - Project guidance for Claude
- `README.md` - Project overview and setup instructions

**Determine TypeScript vs JavaScript:**
- If `tsconfig.json` exists → TypeScript
- If only `package.json` with `.js` files → JavaScript

### Phase 2: Framework Detection

Read dependency files to detect frameworks:

**JavaScript/TypeScript** (check `package.json` dependencies):
- `react`, `react-dom` → React
- `next` → Next.js
- `vue` → Vue
- `nuxt` → Nuxt
- `@angular/core` → Angular
- `svelte` → Svelte
- `express` → Express

**Python** (check `pyproject.toml` or `requirements.txt`):
- `django` → Django
- `flask` → Flask
- `fastapi` → FastAPI

### Phase 3: Existing Guard Rails Detection

Check for existing configurations to avoid duplication:

**JavaScript/TypeScript:**
| Guard Rail | Existing Config Indicators |
|------------|---------------------------|
| Linting | `eslint.config.js`, `eslint.config.mjs`, `.eslintrc*` |
| Formatting | `.prettierrc*`, `prettier.config.*` |
| Pre-commit | `.husky/`, `package.json` has husky |
| Type check | `tsconfig.json` (for TS projects) |
| Testing | `vitest.config.*`, `jest.config.*`, `*.test.ts` files |
| Dead code | `knip.json`, `knip.config.*` |
| Visual/interaction testing | `playwright.config.*`, `e2e/`, `tests/e2e/`, `*.spec.ts` with Playwright imports |
| CI | `.github/workflows/*.yml` |

**Python:**
| Guard Rail | Existing Config Indicators |
|------------|---------------------------|
| Linting | `[tool.ruff]` in pyproject.toml, `ruff.toml`, `.flake8` |
| Formatting | `[tool.ruff.format]` in pyproject.toml, `[tool.black]` |
| Pre-commit | `.pre-commit-config.yaml` |
| Type check | `[tool.mypy]` in pyproject.toml, `mypy.ini` |
| Testing | `[tool.pytest]` in pyproject.toml, `pytest.ini`, `conftest.py` |
| CI | `.github/workflows/*.yml` |

**Go:**
| Guard Rail | Existing Config Indicators |
|------------|---------------------------|
| Linting | `.golangci.yml`, `.golangci.yaml` |
| Formatting | Built-in (gofmt) |
| Pre-commit | `.pre-commit-config.yaml` |
| Testing | Built-in, `*_test.go` files |
| CI | `.github/workflows/*.yml` |

**Rust:**
| Guard Rail | Existing Config Indicators |
|------------|---------------------------|
| Linting | `clippy.toml`, `.clippy.toml` |
| Formatting | `rustfmt.toml`, `.rustfmt.toml` |
| Pre-commit | `.pre-commit-config.yaml` |
| Testing | Built-in, `#[test]` in code |
| CI | `.github/workflows/*.yml` |

---

## User Questions

After detection, use the AskUserQuestion tool to:

### 1. Confirm Detection

Present what was detected:
- Language/tech stack
- Framework (if any)
- Existing guard rails found
- Missing guard rails identified

Ask user to confirm this is accurate.

### 2. Select Guard Rails to Add

Let user choose which missing tools to add. Present options like:
- Linting (ESLint/Ruff/golangci-lint/Clippy)
- Formatting (Prettier/Ruff format/gofmt/rustfmt)
- Pre-commit hooks (Husky/pre-commit)
- Type checking (already have/mypy)
- Testing framework (Vitest/pytest/built-in)
- Dead code detection (Knip - JS/TS only)
- Visual & interaction testing (Playwright - web projects only)
- CI workflow (GitHub Actions)

### 3. Configuration Preferences (if applicable)

For JS/TS projects adding Prettier:
- Tab width (2 or 4)
- Quotes (single or double)
- Semicolons (yes or no)

---

## Guard Rails by Tech Stack

### TypeScript/JavaScript

#### ESLint (Flat Config)

**eslint.config.js:**
```javascript
import eslint from '@eslint/js';
import tseslint from 'typescript-eslint';
import prettier from 'eslint-config-prettier';
import globals from 'globals';

export default tseslint.config(
    eslint.configs.recommended,
    ...tseslint.configs.recommended,
    prettier,
    {
        languageOptions: {
            ecmaVersion: 2022,
            sourceType: 'module',
            globals: {
                ...globals.browser,
                ...globals.node,
            },
        },
        rules: {
            'no-unused-vars': 'off',
            '@typescript-eslint/no-unused-vars': ['warn', {
                argsIgnorePattern: '^_',
                varsIgnorePattern: '^_',
                caughtErrorsIgnorePattern: '^_',
            }],
            'no-console': 'warn',
            'prefer-const': 'warn',
            'no-var': 'error',
        },
    },
    {
        ignores: ['dist/', 'coverage/', 'node_modules/', 'build/'],
    }
);
```

**For React projects, add:**
```javascript
import react from 'eslint-plugin-react';
import reactHooks from 'eslint-plugin-react-hooks';

// Add to config array:
{
    plugins: {
        react,
        'react-hooks': reactHooks,
    },
    rules: {
        ...react.configs.recommended.rules,
        ...reactHooks.configs.recommended.rules,
        'react/react-in-jsx-scope': 'off',
        'react/prop-types': 'off',
    },
    settings: {
        react: {
            version: 'detect',
        },
    },
}
```

**For Next.js projects, use:**
```javascript
import eslint from '@eslint/js';
import tseslint from 'typescript-eslint';
import prettier from 'eslint-config-prettier';
import nextPlugin from '@next/eslint-plugin-next';
import globals from 'globals';

export default tseslint.config(
    eslint.configs.recommended,
    ...tseslint.configs.recommended,
    prettier,
    {
        plugins: {
            '@next/next': nextPlugin,
        },
        rules: {
            ...nextPlugin.configs.recommended.rules,
            ...nextPlugin.configs['core-web-vitals'].rules,
        },
    },
    {
        languageOptions: {
            ecmaVersion: 2022,
            sourceType: 'module',
            globals: {
                ...globals.browser,
                ...globals.node,
            },
        },
        rules: {
            'no-unused-vars': 'off',
            '@typescript-eslint/no-unused-vars': ['warn', {
                argsIgnorePattern: '^_',
                varsIgnorePattern: '^_',
            }],
            'no-console': 'warn',
        },
    },
    {
        ignores: ['dist/', '.next/', 'node_modules/', 'out/'],
    }
);
```

**Required devDependencies:**
```json
{
  "@eslint/js": "^9.0.0",
  "eslint": "^9.0.0",
  "eslint-config-prettier": "^10.0.0",
  "globals": "^16.0.0",
  "typescript-eslint": "^8.0.0"
}
```

**Additional for React:**
```json
{
  "eslint-plugin-react": "^7.0.0",
  "eslint-plugin-react-hooks": "^5.0.0"
}
```

**Additional for Next.js:**
```json
{
  "@next/eslint-plugin-next": "^15.0.0"
}
```

#### Prettier

**.prettierrc.json:**
```json
{
  "semi": true,
  "singleQuote": true,
  "tabWidth": 4,
  "trailingComma": "es5",
  "printWidth": 100,
  "arrowParens": "avoid",
  "endOfLine": "lf"
}
```

**.prettierignore:**
```
dist/
coverage/
node_modules/
build/
.next/
*.min.js
pnpm-lock.yaml
package-lock.json
yarn.lock
```

**Required devDependencies:**
```json
{
  "prettier": "^3.0.0"
}
```

#### Husky + lint-staged

**package.json additions:**
```json
{
  "scripts": {
    "prepare": "husky"
  },
  "lint-staged": {
    "*.{js,ts,tsx,jsx}": [
      "eslint --fix",
      "prettier --write"
    ],
    "*.{json,md,yml,yaml}": [
      "prettier --write"
    ]
  }
}
```

**.husky/pre-commit:**
```bash
npm run typecheck
npx lint-staged
```

**Required devDependencies:**
```json
{
  "husky": "^9.0.0",
  "lint-staged": "^15.0.0"
}
```

**Setup commands:**
```bash
npx husky init
```

Then create `.husky/pre-commit` using Write tool (not echo).

#### Knip (Dead Code Detection)

**knip.json:**
```json
{
  "entry": ["src/index.ts", "src/main.ts"],
  "project": ["src/**/*.{ts,tsx,js,jsx}"],
  "ignore": ["**/*.test.ts", "**/*.spec.ts", "**/*.d.ts"]
}
```

**For Next.js:**
```json
{
  "entry": ["app/**/*.{ts,tsx}", "pages/**/*.{ts,tsx}"],
  "project": ["**/*.{ts,tsx}"],
  "ignore": ["**/*.test.ts", "**/*.spec.ts"]
}
```

**package.json script:**
```json
{
  "scripts": {
    "knip": "knip"
  }
}
```

**Required devDependencies:**
```json
{
  "knip": "^5.0.0"
}
```

#### Vitest

**vitest.config.ts:**
```typescript
import { defineConfig } from 'vitest/config';
import tsconfigPaths from 'vite-tsconfig-paths';

export default defineConfig({
    plugins: [tsconfigPaths()],
    test: {
        globals: true,
        environment: 'jsdom',
        include: ['src/**/*.{test,spec}.{ts,tsx}', 'tests/**/*.{test,spec}.{ts,tsx}'],
        coverage: {
            provider: 'v8',
            reporter: ['text', 'html'],
            exclude: [
                'node_modules/',
                'dist/',
                '**/*.test.ts',
                '**/*.spec.ts',
            ],
        },
    },
});
```

**For React projects:**
```typescript
import { defineConfig } from 'vitest/config';
import react from '@vitejs/plugin-react';
import tsconfigPaths from 'vite-tsconfig-paths';

export default defineConfig({
    plugins: [react(), tsconfigPaths()],
    test: {
        globals: true,
        environment: 'jsdom',
        setupFiles: ['./src/test/setup.ts'],
        include: ['src/**/*.{test,spec}.{ts,tsx}'],
        coverage: {
            provider: 'v8',
            reporter: ['text', 'html'],
        },
    },
});
```

**src/test/setup.ts (for React):**
```typescript
import '@testing-library/jest-dom/vitest';
```

**package.json scripts:**
```json
{
  "scripts": {
    "test": "vitest",
    "test:run": "vitest run",
    "test:coverage": "vitest run --coverage"
  }
}
```

**Required devDependencies:**
```json
{
  "vitest": "^2.0.0",
  "@vitest/coverage-v8": "^2.0.0",
  "jsdom": "^25.0.0",
  "vite-tsconfig-paths": "^5.0.0"
}
```

**Additional for React:**
```json
{
  "@testing-library/react": "^16.0.0",
  "@testing-library/jest-dom": "^6.0.0"
}
```

#### Playwright (Visual & Interaction Testing)

**Only add when the project is a website** - i.e. has a `dev` or `start` script that serves HTML, uses a web framework (React, Next.js, Vue, Svelte, Angular), or has an `index.html` entry point. Skip for pure libraries, CLIs, or API-only backends.

**playwright.config.ts:**
```typescript
import { defineConfig, devices } from '@playwright/test';

export default defineConfig({
    testDir: './e2e',
    fullyParallel: true,
    forbidOnly: !!process.env.CI,
    retries: process.env.CI ? 2 : 0,
    workers: process.env.CI ? 1 : undefined,
    reporter: process.env.CI ? 'github' : 'html',
    use: {
        baseURL: process.env.BASE_URL || 'http://localhost:3000',
        trace: 'on-first-retry',
        screenshot: 'only-on-failure',
    },
    projects: [
        {
            name: 'chromium',
            use: { ...devices['Desktop Chrome'] },
        },
        {
            name: 'firefox',
            use: { ...devices['Desktop Firefox'] },
        },
        {
            name: 'webkit',
            use: { ...devices['Desktop Safari'] },
        },
    ],
    webServer: {
        command: 'npm run dev',
        url: 'http://localhost:3000',
        reuseExistingServer: !process.env.CI,
        timeout: 120_000,
    },
});
```

**For Next.js projects, adjust `webServer`:**
```typescript
webServer: {
    command: 'npm run dev',
    url: 'http://localhost:3000',
    reuseExistingServer: !process.env.CI,
    timeout: 120_000,
},
```

**For Vite projects, adjust `webServer` and `baseURL`:**
```typescript
use: {
    baseURL: process.env.BASE_URL || 'http://localhost:5173',
},
webServer: {
    command: 'npm run dev',
    url: 'http://localhost:5173',
    reuseExistingServer: !process.env.CI,
    timeout: 120_000,
},
```

**e2e/visual.spec.ts (Visual Regression Baseline):**
```typescript
import { test, expect } from '@playwright/test';

test.describe('Visual regression', () => {
    test('homepage matches screenshot', async ({ page }) => {
        await page.goto('/');
        await page.waitForLoadState('networkidle');
        await expect(page).toHaveScreenshot('homepage.png', {
            fullPage: true,
            maxDiffPixelRatio: 0.01,
        });
    });

    // Add additional pages/routes as needed:
    // test('about page matches screenshot', async ({ page }) => {
    //     await page.goto('/about');
    //     await page.waitForLoadState('networkidle');
    //     await expect(page).toHaveScreenshot('about.png', {
    //         fullPage: true,
    //         maxDiffPixelRatio: 0.01,
    //     });
    // });
});
```

**e2e/interaction.spec.ts (Interaction Smoke Tests):**
```typescript
import { test, expect } from '@playwright/test';

test.describe('Core interactions', () => {
    test('page loads and is interactive', async ({ page }) => {
        await page.goto('/');
        await page.waitForLoadState('networkidle');

        // Verify the page has a title
        await expect(page).toHaveTitle(/.+/);

        // Verify main content is visible
        const body = page.locator('body');
        await expect(body).toBeVisible();
    });

    test('navigation links work', async ({ page }) => {
        await page.goto('/');
        await page.waitForLoadState('networkidle');

        // Find all navigation links and verify they respond to clicks
        const navLinks = page.locator('nav a, header a');
        const linkCount = await navLinks.count();

        if (linkCount > 0) {
            // Click first nav link and verify navigation occurs
            const firstLink = navLinks.first();
            const href = await firstLink.getAttribute('href');
            if (href && !href.startsWith('http') && !href.startsWith('#')) {
                await firstLink.click();
                await page.waitForLoadState('networkidle');
                // Verify we navigated (URL changed or content loaded)
                await expect(page.locator('body')).toBeVisible();
            }
        }
    });

    test('buttons are clickable and respond', async ({ page }) => {
        await page.goto('/');
        await page.waitForLoadState('networkidle');

        const buttons = page.locator('button:visible');
        const buttonCount = await buttons.count();

        if (buttonCount > 0) {
            // Verify first visible button is enabled and clickable
            const firstButton = buttons.first();
            await expect(firstButton).toBeEnabled();
        }
    });

    test('no console errors on page load', async ({ page }) => {
        const errors: string[] = [];
        page.on('console', msg => {
            if (msg.type() === 'error') {
                errors.push(msg.text());
            }
        });

        await page.goto('/');
        await page.waitForLoadState('networkidle');

        expect(errors).toEqual([]);
    });

    test('no accessibility violations in tab order', async ({ page }) => {
        await page.goto('/');
        await page.waitForLoadState('networkidle');

        // Verify keyboard navigation works - Tab through interactive elements
        await page.keyboard.press('Tab');
        const focusedElement = page.locator(':focus');
        // At least one element should be focusable
        await expect(focusedElement).toBeVisible();
    });
});
```

**Notes on visual regression workflow:**
- First run creates baseline screenshots in `e2e/visual.spec.ts-snapshots/`
- **Baseline snapshots MUST be committed to git** so CI can compare against them
- To update baselines after intentional UI changes: `npx playwright test --update-snapshots`
- `maxDiffPixelRatio: 0.01` allows 1% pixel variance to handle anti-aliasing differences across environments
- The interaction tests are smoke tests - instruct the user to expand them with project-specific flows (forms, modals, auth, etc.)

**package.json scripts:**
```json
{
  "scripts": {
    "test:e2e": "playwright test",
    "test:e2e:ui": "playwright test --ui",
    "test:e2e:update-snapshots": "playwright test --update-snapshots"
  }
}
```

**Required devDependencies:**
```json
{
  "@playwright/test": "^1.48.0"
}
```

**Setup commands:**
```bash
npx playwright install --with-deps
```

**.gitignore additions:**
```
# Playwright
test-results/
playwright-report/
blob-report/
```

**Do NOT gitignore** the snapshot directories (`e2e/**/*-snapshots/`) - these baselines must be committed.

#### GitHub Actions CI

**.github/workflows/ci.yml:**
```yaml
name: CI

on:
  push:
    branches: [main, master]
  pull_request:
    branches: [main, master]

jobs:
  check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Type check
        run: npm run typecheck

      - name: Lint
        run: npm run lint

      - name: Format check
        run: npm run format:check

      - name: Find unused code
        run: npm run knip

      - name: Run tests
        run: npm run test:run

      - name: Security audit
        run: npm audit --audit-level=high

  e2e:
    runs-on: ubuntu-latest
    needs: check
    steps:
      - uses: actions/checkout@v4

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'

      - name: Install dependencies
        run: npm ci

      - name: Install Playwright browsers
        run: npx playwright install --with-deps

      - name: Run Playwright tests
        run: npm run test:e2e

      - name: Upload test results
        uses: actions/upload-artifact@v4
        if: ${{ !cancelled() }}
        with:
          name: playwright-report
          path: playwright-report/
          retention-days: 30
```

**For pnpm projects:**
```yaml
name: CI

on:
  push:
    branches: [main, master]
  pull_request:
    branches: [main, master]

jobs:
  check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup pnpm
        uses: pnpm/action-setup@v4
        with:
          version: 9

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'pnpm'

      - name: Install dependencies
        run: pnpm install --frozen-lockfile

      - name: Type check
        run: pnpm typecheck

      - name: Lint
        run: pnpm lint

      - name: Format check
        run: pnpm format:check

      - name: Find unused code
        run: pnpm knip

      - name: Run tests
        run: pnpm test:run

  e2e:
    runs-on: ubuntu-latest
    needs: check
    steps:
      - uses: actions/checkout@v4

      - name: Setup pnpm
        uses: pnpm/action-setup@v4
        with:
          version: 9

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'pnpm'

      - name: Install dependencies
        run: pnpm install --frozen-lockfile

      - name: Install Playwright browsers
        run: pnpm exec playwright install --with-deps

      - name: Run Playwright tests
        run: pnpm test:e2e

      - name: Upload test results
        uses: actions/upload-artifact@v4
        if: ${{ !cancelled() }}
        with:
          name: playwright-report
          path: playwright-report/
          retention-days: 30
```

**Note:** Only include the `e2e` job when the project is a website with Playwright configured. Omit it for libraries, CLIs, and API-only backends.

---

### Python

#### Ruff (Linting + Formatting)

**pyproject.toml additions:**
```toml
[tool.ruff]
target-version = "py312"
line-length = 100
src = ["src", "tests"]

[tool.ruff.lint]
select = [
    "E",      # pycodestyle errors
    "W",      # pycodestyle warnings
    "F",      # Pyflakes
    "I",      # isort
    "B",      # flake8-bugbear
    "C4",     # flake8-comprehensions
    "UP",     # pyupgrade
    "ARG",    # flake8-unused-arguments
    "SIM",    # flake8-simplify
]
ignore = [
    "E501",   # line too long (handled by formatter)
]

[tool.ruff.lint.isort]
known-first-party = ["src"]

[tool.ruff.format]
quote-style = "double"
indent-style = "space"
skip-magic-trailing-comma = false
line-ending = "auto"
```

**For Django projects, add:**
```toml
[tool.ruff.lint]
select = [
    "E", "W", "F", "I", "B", "C4", "UP", "ARG", "SIM",
    "DJ",     # flake8-django
]

[tool.ruff.lint.per-file-ignores]
"*/migrations/*.py" = ["E501"]
"*/settings/*.py" = ["F401", "F403"]
```

**For FastAPI projects, add:**
```toml
[tool.ruff.lint]
select = [
    "E", "W", "F", "I", "B", "C4", "UP", "ARG", "SIM",
    "FAST",   # FastAPI
]
```

#### mypy (Type Checking)

**pyproject.toml additions:**
```toml
[tool.mypy]
python_version = "3.12"
strict = true
warn_return_any = true
warn_unused_ignores = true
disallow_untyped_defs = true
disallow_incomplete_defs = true
check_untyped_defs = true
no_implicit_optional = true

[[tool.mypy.overrides]]
module = "tests.*"
disallow_untyped_defs = false
```

**For Django projects:**
```toml
[tool.mypy]
python_version = "3.12"
strict = true
plugins = ["mypy_django_plugin.main"]

[tool.django-stubs]
django_settings_module = "myproject.settings"

[[tool.mypy.overrides]]
module = "*.migrations.*"
ignore_errors = true
```

#### pytest

**pyproject.toml additions:**
```toml
[tool.pytest.ini_options]
testpaths = ["tests"]
python_files = ["test_*.py", "*_test.py"]
python_functions = ["test_*"]
addopts = "-v --tb=short"
filterwarnings = [
    "ignore::DeprecationWarning",
]
```

**For Django projects:**
```toml
[tool.pytest.ini_options]
DJANGO_SETTINGS_MODULE = "myproject.settings"
testpaths = ["tests"]
python_files = ["test_*.py"]
addopts = "-v --tb=short --reuse-db"
```

**For FastAPI projects:**
```toml
[tool.pytest.ini_options]
testpaths = ["tests"]
python_files = ["test_*.py"]
addopts = "-v --tb=short"
asyncio_mode = "auto"
```

#### pre-commit

**.pre-commit-config.yaml:**
```yaml
repos:
  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.8.0
    hooks:
      - id: ruff
        args: [--fix]
      - id: ruff-format

  - repo: https://github.com/pre-commit/mirrors-mypy
    rev: v1.13.0
    hooks:
      - id: mypy
        additional_dependencies: []

  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v5.0.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
      - id: check-added-large-files
```

**For Django, add to mypy additional_dependencies:**
```yaml
additional_dependencies:
  - django-stubs
```

**Setup command:**
```bash
pre-commit install
```

#### GitHub Actions CI

**.github/workflows/ci.yml:**
```yaml
name: CI

on:
  push:
    branches: [main, master]
  pull_request:
    branches: [main, master]

jobs:
  check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.12'

      - name: Install uv
        uses: astral-sh/setup-uv@v4

      - name: Install dependencies
        run: uv sync

      - name: Lint
        run: uv run ruff check .

      - name: Format check
        run: uv run ruff format --check .

      - name: Type check
        run: uv run mypy .

      - name: Run tests
        run: uv run pytest
```

**For pip-based projects:**
```yaml
name: CI

on:
  push:
    branches: [main, master]
  pull_request:
    branches: [main, master]

jobs:
  check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.12'
          cache: 'pip'

      - name: Install dependencies
        run: |
          pip install -r requirements.txt
          pip install ruff mypy pytest

      - name: Lint
        run: ruff check .

      - name: Format check
        run: ruff format --check .

      - name: Type check
        run: mypy .

      - name: Run tests
        run: pytest
```

---

### Go

#### golangci-lint

**.golangci.yml:**
```yaml
run:
  timeout: 5m
  tests: true

linters:
  enable:
    - errcheck
    - gosimple
    - govet
    - ineffassign
    - staticcheck
    - unused
    - gofmt
    - goimports
    - misspell
    - unconvert
    - unparam
    - gocritic

linters-settings:
  gofmt:
    simplify: true
  goimports:
    local-prefixes: github.com/yourorg/yourrepo
  gocritic:
    enabled-tags:
      - diagnostic
      - style
      - performance

issues:
  exclude-use-default: false
  max-issues-per-linter: 0
  max-same-issues: 0
```

#### pre-commit

**.pre-commit-config.yaml:**
```yaml
repos:
  - repo: https://github.com/golangci/golangci-lint
    rev: v1.62.0
    hooks:
      - id: golangci-lint

  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v5.0.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
      - id: check-added-large-files

  - repo: local
    hooks:
      - id: go-fmt
        name: go fmt
        entry: gofmt -w -s
        language: system
        types: [go]
      - id: go-test
        name: go test
        entry: go test ./...
        language: system
        pass_filenames: false
```

**Setup command:**
```bash
pre-commit install
```

#### GitHub Actions CI

**.github/workflows/ci.yml:**
```yaml
name: CI

on:
  push:
    branches: [main, master]
  pull_request:
    branches: [main, master]

jobs:
  check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup Go
        uses: actions/setup-go@v5
        with:
          go-version: '1.23'

      - name: golangci-lint
        uses: golangci/golangci-lint-action@v6
        with:
          version: latest

      - name: Run tests
        run: go test -v -race -coverprofile=coverage.out ./...

      - name: Build
        run: go build -v ./...
```

---

### Rust

#### Clippy Configuration

**clippy.toml:**
```toml
avoid-breaking-exported-api = false
cognitive-complexity-threshold = 30
```

**Cargo.toml additions for stricter lints:**
```toml
[lints.rust]
unsafe_code = "forbid"

[lints.clippy]
all = "warn"
pedantic = "warn"
nursery = "warn"
cargo = "warn"
```

#### rustfmt Configuration

**rustfmt.toml:**
```toml
edition = "2021"
max_width = 100
tab_spaces = 4
use_small_heuristics = "Default"
newline_style = "Auto"
```

#### pre-commit

**.pre-commit-config.yaml:**
```yaml
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v5.0.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
      - id: check-added-large-files

  - repo: local
    hooks:
      - id: cargo-fmt
        name: cargo fmt
        entry: cargo fmt --all --
        language: system
        types: [rust]
        pass_filenames: false
      - id: cargo-clippy
        name: cargo clippy
        entry: cargo clippy --all-targets --all-features -- -D warnings
        language: system
        types: [rust]
        pass_filenames: false
      - id: cargo-test
        name: cargo test
        entry: cargo test
        language: system
        types: [rust]
        pass_filenames: false
```

**Setup command:**
```bash
pre-commit install
```

#### GitHub Actions CI

**.github/workflows/ci.yml:**
```yaml
name: CI

on:
  push:
    branches: [main, master]
  pull_request:
    branches: [main, master]

env:
  CARGO_TERM_COLOR: always

jobs:
  check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Setup Rust
        uses: dtolnay/rust-toolchain@stable
        with:
          components: rustfmt, clippy

      - name: Cache cargo
        uses: Swatinem/rust-cache@v2

      - name: Check formatting
        run: cargo fmt --all -- --check

      - name: Clippy
        run: cargo clippy --all-targets --all-features -- -D warnings

      - name: Run tests
        run: cargo test --all-features

      - name: Build
        run: cargo build --release
```

---

## Post-Setup Instructions

After generating the guard rail files, provide these instructions based on tech stack:

### JavaScript/TypeScript

```bash
# Install new dependencies
npm install

# Initialize husky (if added)
npx husky init
# Then create .husky/pre-commit with Write tool

# Verify setup
npm run check   # or create this script
npm run lint
npm run format:check
npm run typecheck
npm test

# If Playwright was added (web projects only):
npx playwright install --with-deps
npm run test:e2e  # first run creates baseline screenshots
# Commit the snapshot baselines: git add e2e/**/*-snapshots/
```

### Python

```bash
# Install dependencies (if using uv)
uv sync

# Or with pip
pip install -r requirements-dev.txt

# Install pre-commit hooks
pre-commit install

# Verify setup
ruff check .
ruff format --check .
mypy .
pytest
```

### Go

```bash
# Install golangci-lint
go install github.com/golangci/golangci-lint/cmd/golangci-lint@latest

# Install pre-commit
pip install pre-commit
pre-commit install

# Verify setup
golangci-lint run
go test ./...
```

### Rust

```bash
# Install pre-commit
pip install pre-commit
pre-commit install

# Verify setup
cargo fmt --all -- --check
cargo clippy --all-targets --all-features -- -D warnings
cargo test
```

---

## Example Workflow

1. **User invokes**: `/cb-guard-rails`

2. **Detection phase**: Read package.json/pyproject.toml/go.mod/Cargo.toml and detect:
   - Language: TypeScript
   - Framework: Next.js
   - Existing: ESLint (old config), no Prettier, no Husky

3. **Present findings**: Use AskUserQuestion to show detection and ask which missing tools to add

4. **Generate files**: Only add:
   - Upgrade ESLint to flat config
   - Add .prettierrc.json + .prettierignore
   - Add Husky + lint-staged
   - Add knip.json
   - Add Playwright config + baseline visual/interaction tests (website detected)
   - Add .github/workflows/ci.yml (if missing, includes e2e job for websites)

5. **Post-setup**: Provide commands to run

---

## Important Notes

- **Never duplicate**: Always check for existing configs before adding new ones
- **Respect existing patterns**: If project uses tabs, keep tabs. If uses 2 spaces, keep 2 spaces.
- **Framework-aware**: Use framework-specific configs when applicable
- **Minimal changes**: Only add what's explicitly requested and confirmed by user
- **Website detection**: Only add Playwright when the project builds/serves a website (has `dev`/`start` script, web framework, or `index.html`). Never add it for pure libraries, CLIs, or API-only backends.
- **Snapshot baselines**: When Playwright visual tests are added, the first run creates baseline screenshots. These MUST be committed to git. Instruct the user to run `npx playwright test --update-snapshots` after intentional UI changes.
- **Test after setup**: Always verify the guard rails work before finishing
