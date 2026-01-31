---
name: cb-new-project
author: ChrisBrooksbank
description: Scaffold a new TypeScript project with LLM guardrails - ESLint, Prettier, Husky, Knip, Vitest, and CI. Creates tight feedback loops that catch errors early.
---

This skill scaffolds TypeScript projects with "guardrails" - tools that create tight feedback loops to catch errors early, especially valuable when working with LLMs.

## Philosophy

| Tool | Catches |
|------|---------|
| TypeScript (strict) | Type errors, null issues, unused variables |
| ESLint | Code patterns, potential bugs |
| Prettier | Style inconsistencies |
| Husky + lint-staged | Stops bad code before commit |
| Knip | Unused exports, dependencies, files |
| Vitest | Behavioral regressions |
| GitHub Actions | Issues missed locally |

## When to Use

- Creating a new TypeScript project from scratch
- User asks to "set up a project with good tooling"
- Starting a project that will involve ongoing LLM assistance

## Before Scaffolding

**Important**: Before generating any files, use the AskUserQuestion tool to gather these details. Provide the context shown below to help the user decide:

### Required

1. **Project name** - Used in package.json, HTML title, README, and CLAUDE.md
2. **Project description** - One-line summary for package.json and README

### Project Structure

3. **Simple or Modular structure?**
   - Simple: Flat `src/` folder, good for small projects or scripts
   - Modular: Organized folders (`api/`, `core/`, `utils/`, `types/`, `config/`), good for larger apps
   - *Choose Modular if: project will grow, has multiple features, or calls external APIs*

### Optional Features

4. **PWA support?**
   - Yes: App works offline, installable on devices, caches assets automatically
   - No: Standard web app, requires internet connection
   - *Choose Yes if: building a dashboard, tool, or app users access repeatedly*

5. **Zod + Config facade?**
   - Yes: Type-safe config loading, validates external data, helpful error messages, includes `getConfig()` pattern
   - No: Handle config and validation manually
   - *Choose Yes if: loading JSON config files, calling external APIs, or need runtime type safety*

6. **Logger + Helper utilities?**
   - Yes: Adds Logger (levels, timestamps), retryWithBackoff, debounce, throttle, IntervalManager
   - No: Use console.log and write utilities as needed
   - *Choose Yes if: building a long-running app, need retry logic for APIs, or want consistent logging*

Only proceed with file generation after receiving answers. Tailor the output based on responses.

---

## File Checklist

Generate only the files needed based on user selections:

### Always Create
- `index.html`
- `src/main.ts`
- `src/main.test.ts`
- `package.json`
- `tsconfig.json`
- `vite.config.ts`
- `vitest.config.ts`
- `eslint.config.js`
- `knip.json`
- `.prettierrc.json`
- `.prettierignore`
- `.gitignore`
- `.env.example`
- `README.md`
- `CLAUDE.md`
- `.husky/pre-commit`
- `.github/workflows/ci.yml`

### If Modular Structure
- `src/types/index.ts`
- `src/api/index.ts`
- `src/core/index.ts`

### If Logger + Helpers Selected
- `src/utils/index.ts`
- `src/utils/logger.ts`
- `src/utils/helpers.ts`

### If Zod + Config Selected
- `src/config/index.ts`
- `src/config/schema.ts`
- `src/config/loader.ts`
- `app.config.example.json`

### If PWA Selected
- `public/icons/` (directory - remind user to add icons)

---

## Project Structure

### Simple Structure

```
project/
├── src/
│   ├── main.ts
│   └── main.test.ts
├── index.html
├── package.json
├── tsconfig.json
├── vite.config.ts
├── vitest.config.ts
├── eslint.config.js
├── knip.json
├── .prettierrc.json
├── .prettierignore
├── .gitignore
├── .env.example
├── README.md
├── CLAUDE.md
├── .husky/
│   └── pre-commit
└── .github/
    └── workflows/
        └── ci.yml
```

### Modular Structure

```
project/
├── src/
│   ├── main.ts
│   ├── main.test.ts
│   ├── api/
│   │   └── index.ts
│   ├── core/
│   │   └── index.ts
│   ├── config/         # If Zod selected
│   │   ├── index.ts
│   │   ├── schema.ts
│   │   └── loader.ts
│   ├── utils/          # If Logger selected
│   │   ├── index.ts
│   │   ├── logger.ts
│   │   └── helpers.ts
│   └── types/
│       └── index.ts
├── index.html
├── package.json
├── tsconfig.json
├── vite.config.ts
├── vitest.config.ts
├── eslint.config.js
├── knip.json
├── .prettierrc.json
├── .prettierignore
├── .gitignore
├── .env.example
├── README.md
├── CLAUDE.md
├── .husky/
│   └── pre-commit
└── .github/
    └── workflows/
        └── ci.yml
```

---

## Core Files

### index.html

Replace `PROJECT_NAME` with the actual project name:

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>PROJECT_NAME</title>
    <style>
      * {
        margin: 0;
        padding: 0;
        box-sizing: border-box;
      }
      body {
        font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
        min-height: 100vh;
        display: flex;
        flex-direction: column;
        background: #f5f5f5;
        color: #333;
      }
      header {
        background: #2563eb;
        color: white;
        padding: 1rem 2rem;
        box-shadow: 0 2px 4px rgba(0,0,0,0.1);
      }
      header h1 {
        font-size: 1.5rem;
        font-weight: 600;
      }
      main {
        flex: 1;
        padding: 2rem;
        max-width: 800px;
        margin: 0 auto;
        width: 100%;
      }
      .card {
        background: white;
        border-radius: 8px;
        padding: 1.5rem;
        box-shadow: 0 1px 3px rgba(0,0,0,0.1);
        margin-bottom: 1rem;
      }
      .card h2 {
        font-size: 1.25rem;
        margin-bottom: 0.5rem;
        color: #1f2937;
      }
      .card p {
        color: #6b7280;
        line-height: 1.6;
      }
      .status {
        display: inline-block;
        padding: 0.25rem 0.75rem;
        border-radius: 9999px;
        font-size: 0.875rem;
        font-weight: 500;
        background: #d1fae5;
        color: #065f46;
      }
      footer {
        text-align: center;
        padding: 1rem;
        color: #9ca3af;
        font-size: 0.875rem;
      }
    </style>
  </head>
  <body>
    <header>
      <h1>PROJECT_NAME</h1>
    </header>
    <main id="app">
      <div class="card">
        <h2>Welcome</h2>
        <p>Your project is ready. Edit <code>src/main.ts</code> to get started.</p>
      </div>
      <div class="card">
        <h2>Status</h2>
        <p><span class="status">Running</span></p>
      </div>
    </main>
    <footer>
      Built with TypeScript + Vite
    </footer>
    <script type="module" src="/src/main.ts"></script>
  </body>
</html>
```

### README.md

Replace `PROJECT_NAME` and `PROJECT_DESCRIPTION`:

```markdown
# PROJECT_NAME

PROJECT_DESCRIPTION

## Getting Started

```bash
npm install
npm run dev
```

## Scripts

| Command | Description |
|---------|-------------|
| `npm run dev` | Start development server |
| `npm run build` | Production build |
| `npm run preview` | Preview production build |
| `npm test` | Run tests in watch mode |
| `npm run test:run` | Run tests once |
| `npm run test:coverage` | Generate coverage report |
| `npm run lint` | Check for lint errors |
| `npm run lint:fix` | Fix lint errors |
| `npm run format` | Format code with Prettier |
| `npm run typecheck` | TypeScript type checking |
| `npm run knip` | Find unused code |
| `npm run check` | Run all checks |

## Project Structure

```
src/
├── main.ts          # Application entry point
├── main.test.ts     # Example test
└── ...
```

## License

MIT
```

### src/main.ts (Simple)

```typescript
console.log('Application starting...');

const app = document.getElementById('app');

if (app) {
    console.log('Application mounted');
}
```

### src/main.ts (Modular, with Logger)

```typescript
import { Logger } from '@utils/logger';

Logger.info('Application starting...');

const app = document.getElementById('app');

if (app) {
    Logger.success('Application mounted');
}
```

### src/main.ts (Modular, with Logger + Zod)

```typescript
import { loadConfig, getConfig } from '@config';
import { Logger } from '@utils/logger';

async function init() {
    try {
        const config = await loadConfig();
        Logger.setDebugMode(config.debug);
        Logger.success('Configuration loaded');

        const app = document.getElementById('app');
        if (app) {
            Logger.success('Application mounted');
        }
    } catch (error) {
        Logger.error('Failed to initialize:', String(error));
    }
}

init();
```

### src/main.test.ts

```typescript
import { describe, it, expect } from 'vitest';

describe('Application', () => {
    it('should pass a basic test', () => {
        expect(1 + 1).toBe(2);
    });

    it('should handle strings', () => {
        const greeting = 'Hello, World!';
        expect(greeting).toContain('Hello');
    });
});
```

### CLAUDE.md

Generate this file with sections filled in based on user selections. Do NOT include the bracketed instructions in the final output:

```markdown
# CLAUDE.md

This file provides guidance to Claude Code when working with this codebase.

## Project Overview

PROJECT_DESCRIPTION

## Development Commands

```bash
npm run dev            # Start Vite dev server
npm run build          # Production build
npm run preview        # Preview production build
npm test               # Vitest watch mode
npm run test:run       # Run tests once
npm run test:coverage  # Coverage report
npm run lint           # ESLint check
npm run lint:fix       # ESLint auto-fix
npm run format         # Prettier format all files
npm run typecheck      # TypeScript type check
npm run knip           # Find unused code
npm run check          # Run all checks
```

## Architecture

### TypeScript with ES Modules

Path aliases configured:
- `@/*` → `src/*`
- `@api/*` → `src/api/*`
- `@core/*` → `src/core/*`
- `@utils/*` → `src/utils/*`
- `@config/*` → `src/config/*`
- `@types/*` → `src/types/*`

### Key Patterns

Use Logger instead of console.log:
```typescript
import { Logger } from '@utils/logger';
Logger.info('Message');
Logger.warn('Warning');
Logger.error('Error');
Logger.success('Done');
Logger.debug('Debug');  // Only in debug mode
```

Config is loaded asynchronously and validated with Zod:
```typescript
import { loadConfig, getConfig } from '@config';

// At startup (once):
await loadConfig();

// After initialization:
const config = getConfig();
```

Retry logic for flaky operations:
```typescript
import { retryWithBackoff } from '@utils/helpers';
const data = await retryWithBackoff(() => fetchData());
```
```

**Note**: When generating CLAUDE.md, only include the sections relevant to the user's selections. For Simple structure, use only `@/*` alias. Remove Logger/Config/helpers sections if those options weren't selected.

### package.json

Add `jsdom` for testing and `zod`/`vite-plugin-pwa` based on selections:

```json
{
  "name": "project-name",
  "version": "1.0.0",
  "description": "Project description",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "preview": "vite preview",
    "test": "vitest",
    "test:run": "vitest run",
    "test:coverage": "vitest run --coverage",
    "lint": "eslint .",
    "lint:fix": "eslint . --fix",
    "format": "prettier --write .",
    "format:check": "prettier --check .",
    "typecheck": "tsc --noEmit",
    "knip": "knip",
    "check": "npm run typecheck && npm run lint && npm run format:check && npm run knip",
    "prepare": "husky"
  },
  "devDependencies": {
    "@eslint/js": "^9.0.0",
    "@types/node": "^22.0.0",
    "@vitest/coverage-v8": "^2.0.0",
    "eslint": "^9.0.0",
    "eslint-config-prettier": "^10.0.0",
    "globals": "^16.0.0",
    "husky": "^9.0.0",
    "jsdom": "^25.0.0",
    "knip": "^5.0.0",
    "lint-staged": "^15.0.0",
    "prettier": "^3.0.0",
    "typescript": "^5.0.0",
    "typescript-eslint": "^8.0.0",
    "vite": "^6.0.0",
    "vite-tsconfig-paths": "^5.0.0",
    "vitest": "^2.0.0"
  },
  "lint-staged": {
    "*.{js,ts,tsx}": [
      "eslint --fix",
      "prettier --write"
    ],
    "*.{json,md,yml,yaml}": [
      "prettier --write"
    ]
  },
  "engines": {
    "node": ">=20.0.0"
  }
}
```

**If Zod selected**, add to dependencies:
```json
"dependencies": {
  "zod": "^3.0.0"
}
```

**If PWA selected**, add to devDependencies:
```json
"vite-plugin-pwa": "^0.20.0"
```

### tsconfig.json (Simple)

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "lib": ["ES2022", "DOM", "DOM.Iterable"],
    "module": "ESNext",
    "moduleResolution": "bundler",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "strict": true,
    "strictNullChecks": true,
    "noImplicitAny": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "declaration": true,
    "sourceMap": true,
    "outDir": "./dist",
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"]
    }
  },
  "include": ["src/**/*.ts"],
  "exclude": ["node_modules", "dist"]
}
```

### tsconfig.json (Modular)

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "lib": ["ES2022", "DOM", "DOM.Iterable"],
    "module": "ESNext",
    "moduleResolution": "bundler",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "esModuleInterop": true,
    "allowSyntheticDefaultImports": true,
    "strict": true,
    "strictNullChecks": true,
    "noImplicitAny": true,
    "noImplicitReturns": true,
    "noFallthroughCasesInSwitch": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "declaration": true,
    "sourceMap": true,
    "outDir": "./dist",
    "baseUrl": ".",
    "paths": {
      "@/*": ["src/*"],
      "@api/*": ["src/api/*"],
      "@core/*": ["src/core/*"],
      "@utils/*": ["src/utils/*"],
      "@config/*": ["src/config/*"],
      "@types/*": ["src/types/*"]
    }
  },
  "include": ["src/**/*.ts"],
  "exclude": ["node_modules", "dist"]
}
```

### vite.config.ts (Base)

```typescript
import { defineConfig } from 'vite';
import tsconfigPaths from 'vite-tsconfig-paths';

export default defineConfig({
    plugins: [tsconfigPaths()],
    build: {
        outDir: 'dist',
        sourcemap: true,
    },
});
```

### vite.config.ts (With PWA)

```typescript
import { defineConfig } from 'vite';
import tsconfigPaths from 'vite-tsconfig-paths';
import { VitePWA } from 'vite-plugin-pwa';

export default defineConfig({
    plugins: [
        tsconfigPaths(),
        VitePWA({
            registerType: 'autoUpdate',
            workbox: {
                globPatterns: ['**/*.{js,css,html,ico,png,svg,woff2}'],
                runtimeCaching: [
                    {
                        urlPattern: /^https:\/\/api\./i,
                        handler: 'NetworkFirst',
                        options: {
                            cacheName: 'api-cache',
                            expiration: {
                                maxEntries: 50,
                                maxAgeSeconds: 300,
                            },
                        },
                    },
                ],
            },
            manifest: {
                name: 'PROJECT_NAME',
                short_name: 'PROJECT_SHORT',
                description: 'PROJECT_DESCRIPTION',
                theme_color: '#2563eb',
                background_color: '#f5f5f5',
                display: 'standalone',
                icons: [
                    {
                        src: '/icons/icon-192.png',
                        sizes: '192x192',
                        type: 'image/png',
                    },
                    {
                        src: '/icons/icon-512.png',
                        sizes: '512x512',
                        type: 'image/png',
                    },
                ],
            },
        }),
    ],
    build: {
        outDir: 'dist',
        sourcemap: true,
    },
});
```

**If PWA selected**, also update index.html `<head>`:
```html
<link rel="manifest" href="/manifest.webmanifest" />
<meta name="theme-color" content="#2563eb" />
<link rel="apple-touch-icon" href="/icons/icon-192.png" />
```

And remind user to create `public/icons/icon-192.png` and `public/icons/icon-512.png`.

### vitest.config.ts

```typescript
import { defineConfig } from 'vitest/config';
import tsconfigPaths from 'vite-tsconfig-paths';

export default defineConfig({
    plugins: [tsconfigPaths()],
    test: {
        globals: true,
        environment: 'jsdom',
        include: ['src/**/*.{test,spec}.ts'],
        coverage: {
            provider: 'v8',
            reporter: ['text', 'html'],
            exclude: [
                'node_modules/',
                'dist/',
                '**/*.test.ts',
                '**/*.spec.ts',
                'src/types/',
            ],
        },
    },
});
```

### eslint.config.js

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
        ignores: ['dist/', 'coverage/', 'node_modules/'],
    }
);
```

### .prettierrc.json

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

### .prettierignore

```
dist/
coverage/
node_modules/
*.min.js
```

### knip.json (Simple)

```json
{
  "entry": ["src/main.ts"],
  "project": ["src/**/*.ts"],
  "ignore": ["**/*.test.ts", "**/*.spec.ts"]
}
```

### knip.json (Modular)

```json
{
  "entry": ["src/main.ts"],
  "project": ["src/**/*.ts"],
  "ignore": ["**/*.test.ts", "**/*.spec.ts"],
  "paths": {
    "@/*": ["src/*"],
    "@api/*": ["src/api/*"],
    "@core/*": ["src/core/*"],
    "@utils/*": ["src/utils/*"],
    "@config/*": ["src/config/*"],
    "@types/*": ["src/types/*"]
  }
}
```

### .gitignore

```
node_modules/
dist/
coverage/
*.log
.env
.env.local
.DS_Store
*.config.json
!*.config.example.json
```

### .env.example

```bash
# Environment variables
# Copy this file to .env and fill in values

# Example:
# API_KEY=your-api-key-here
```

### .husky/pre-commit

Create this file using the Write tool (not shell echo) to avoid Windows line ending issues:

```bash
npm run typecheck
npx lint-staged
```

### .github/workflows/ci.yml

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
```

---

## Modular Structure Files

### src/types/index.ts

Only include LogLevel/LogLevels if Logger is selected but Zod is NOT selected. If Zod is selected, AppConfig comes from the config schema instead.

**If Logger selected, Zod NOT selected:**
```typescript
/**
 * Centralized Type Definitions
 */

export type LogLevel = 'DEBUG' | 'INFO' | 'WARN' | 'ERROR';

export interface LogLevels {
    DEBUG: number;
    INFO: number;
    WARN: number;
    ERROR: number;
}

// Add your shared types here
```

**If Zod selected (with or without Logger):**
```typescript
/**
 * Centralized Type Definitions
 */

// Re-export config types for convenience
export type { AppConfig } from '@config/schema';

// Log levels (if Logger selected)
export type LogLevel = 'DEBUG' | 'INFO' | 'WARN' | 'ERROR';

export interface LogLevels {
    DEBUG: number;
    INFO: number;
    WARN: number;
    ERROR: number;
}

// Add your shared types here
```

**If neither Logger nor Zod selected:**
```typescript
/**
 * Centralized Type Definitions
 */

// Add your shared types here
```

### src/api/index.ts

```typescript
/**
 * API Module
 * External API clients and data fetching
 */

// Example API client structure:
//
// export async function fetchData(endpoint: string): Promise<unknown> {
//     const response = await fetch(endpoint);
//     if (!response.ok) {
//         throw new Error(`API error: ${response.status}`);
//     }
//     return response.json();
// }

export {};
```

### src/core/index.ts

```typescript
/**
 * Core Module
 * Core application logic and business rules
 */

// Example:
//
// export function processData(input: string): string {
//     return input.trim().toLowerCase();
// }

export {};
```

---

## Optional: Logger + Helper Utilities

### src/utils/logger.ts

```typescript
/**
 * Centralized Logging Utility
 * Provides consistent logging with timestamps and log levels
 */

type LogLevel = 'DEBUG' | 'INFO' | 'WARN' | 'ERROR';

interface LogLevels {
    DEBUG: number;
    INFO: number;
    WARN: number;
    ERROR: number;
}

const levels: LogLevels = {
    DEBUG: 0,
    INFO: 1,
    WARN: 2,
    ERROR: 3,
};

let debugMode = false;

export const Logger = {
    levels,
    currentLevel: 1 as number,

    _timestamp(): string {
        return new Date().toISOString().substring(11, 23);
    },

    _format(level: string, emoji: string, message: string, ...args: unknown[]): unknown[] {
        const ts = this._timestamp();
        return [`[${ts}] ${emoji} ${level}:`, message, ...args];
    },

    debug(message: string, ...args: unknown[]): void {
        if (this.currentLevel <= this.levels.DEBUG && debugMode) {
            console.log(...this._format('DEBUG', '🔍', message, ...args));
        }
    },

    info(message: string, ...args: unknown[]): void {
        if (this.currentLevel <= this.levels.INFO) {
            console.log(...this._format('INFO', 'ℹ️', message, ...args));
        }
    },

    warn(message: string, ...args: unknown[]): void {
        if (this.currentLevel <= this.levels.WARN) {
            console.warn(...this._format('WARN', '⚠️', message, ...args));
        }
    },

    error(message: string, ...args: unknown[]): void {
        if (this.currentLevel <= this.levels.ERROR) {
            console.error(...this._format('ERROR', '❌', message, ...args));
        }
    },

    success(message: string, ...args: unknown[]): void {
        if (this.currentLevel <= this.levels.INFO) {
            console.log(...this._format('SUCCESS', '✅', message, ...args));
        }
    },

    setLevel(level: LogLevel): void {
        this.currentLevel = this.levels[level] ?? 1;
    },

    setDebugMode(enabled: boolean): void {
        debugMode = enabled;
    },
};
```

### src/utils/helpers.ts

```typescript
/**
 * Utility Helper Functions
 */

import { Logger } from './logger';

/**
 * Retry an async function with exponential backoff
 */
export async function retryWithBackoff<T>(
    fn: () => Promise<T>,
    maxAttempts = 3,
    initialDelay = 1000
): Promise<T> {
    let lastError: Error | undefined;

    for (let attempt = 1; attempt <= maxAttempts; attempt++) {
        try {
            return await fn();
        } catch (err) {
            lastError = err instanceof Error ? err : new Error(String(err));

            if (attempt === maxAttempts) {
                Logger.error(`Failed after ${maxAttempts} attempts:`, lastError.message);
                throw lastError;
            }

            const backoffDelay = Math.min(initialDelay * Math.pow(2, attempt - 1), 10000);
            Logger.warn(`Attempt ${attempt} failed, retrying in ${backoffDelay}ms...`);
            await new Promise(resolve => setTimeout(resolve, backoffDelay));
        }
    }

    throw lastError;
}

/**
 * Debounce function to limit execution rate
 */
export function debounce<T extends (...args: Parameters<T>) => ReturnType<T>>(
    fn: T,
    delay: number
): (...args: Parameters<T>) => void {
    let timeoutId: ReturnType<typeof setTimeout> | undefined;

    return function (this: ThisParameterType<T>, ...args: Parameters<T>): void {
        clearTimeout(timeoutId);
        timeoutId = setTimeout(() => fn.apply(this, args), delay);
    };
}

/**
 * Throttle function to limit execution frequency
 */
export function throttle<T extends (...args: Parameters<T>) => ReturnType<T>>(
    fn: T,
    limit: number
): (...args: Parameters<T>) => void {
    let inThrottle = false;

    return function (this: ThisParameterType<T>, ...args: Parameters<T>): void {
        if (!inThrottle) {
            fn.apply(this, args);
            inThrottle = true;
            setTimeout(() => (inThrottle = false), limit);
        }
    };
}

/**
 * Interval manager to track and cleanup intervals
 */
class IntervalManagerClass {
    private intervals: ReturnType<typeof setInterval>[] = [];

    register(fn: () => void, delay: number): ReturnType<typeof setInterval> {
        const id = setInterval(fn, delay);
        this.intervals.push(id);
        Logger.debug(`Registered interval with ${delay}ms delay`);
        return id;
    }

    clear(id: ReturnType<typeof setInterval>): void {
        clearInterval(id);
        this.intervals = this.intervals.filter(i => i !== id);
    }

    clearAll(): void {
        Logger.info(`Clearing ${this.intervals.length} intervals`);
        this.intervals.forEach(id => clearInterval(id));
        this.intervals = [];
    }
}

export const IntervalManager = new IntervalManagerClass();

// Cleanup on page unload
if (typeof window !== 'undefined') {
    window.addEventListener('beforeunload', () => {
        IntervalManager.clearAll();
    });
}
```

### src/utils/index.ts

```typescript
export { Logger } from './logger';
export { retryWithBackoff, debounce, throttle, IntervalManager } from './helpers';
```

---

## Optional: Zod + Config Facade

### src/config/schema.ts

```typescript
/**
 * Configuration Schema
 * Define and validate your app configuration
 */

import { z } from 'zod';

export const ConfigSchema = z.object({
    debug: z.boolean().default(false),
    api: z
        .object({
            baseUrl: z.string().url(),
            timeout: z.number().positive().default(5000),
        })
        .optional(),
    // Add more config sections as needed
});

export type AppConfig = z.infer<typeof ConfigSchema>;

export class ConfigValidationError extends Error {
    constructor(
        message: string,
        public errors: z.ZodError
    ) {
        super(message);
        this.name = 'ConfigValidationError';
    }
}
```

### src/config/loader.ts

```typescript
/**
 * Configuration Loader
 * Loads and validates configuration from JSON file
 */

import { ConfigSchema, ConfigValidationError } from './schema';
import type { AppConfig } from './schema';

let config: AppConfig | null = null;

/**
 * Load configuration from JSON file
 * Call once at app startup
 */
export async function loadConfig(path = '/app.config.json'): Promise<AppConfig> {
    try {
        const response = await fetch(path);
        if (!response.ok) {
            throw new Error(`Failed to load config: ${response.status}`);
        }

        const rawConfig = await response.json();
        const result = ConfigSchema.safeParse(rawConfig);

        if (!result.success) {
            console.error('Config validation errors:', result.error.format());
            throw new ConfigValidationError('Invalid configuration', result.error);
        }

        config = result.data;
        return config;
    } catch (error) {
        if (error instanceof ConfigValidationError) {
            throw error;
        }
        // Return defaults if config file not found
        console.warn('Config file not found, using defaults');
        config = ConfigSchema.parse({});
        return config;
    }
}

/**
 * Get loaded configuration
 * Throws if config not loaded yet
 */
export function getConfig(): AppConfig {
    if (!config) {
        throw new Error('Config not loaded. Call loadConfig() first.');
    }
    return config;
}

/**
 * Check if config has been loaded
 */
export function isConfigLoaded(): boolean {
    return config !== null;
}

/**
 * Reset config (useful for testing)
 */
export function resetConfig(): void {
    config = null;
}

/**
 * Set config directly (useful for testing)
 */
export function setConfig(newConfig: AppConfig): void {
    config = newConfig;
}
```

### src/config/index.ts

```typescript
/**
 * Configuration Module
 * Unified access point for all configuration
 */

export { loadConfig, getConfig, isConfigLoaded, resetConfig, setConfig } from './loader';
export { ConfigSchema, ConfigValidationError } from './schema';
export type { AppConfig } from './schema';
```

### app.config.example.json

Create in project root:

```json
{
  "debug": false,
  "api": {
    "baseUrl": "https://api.example.com",
    "timeout": 5000
  }
}
```

---

## Setup

After generating all files, run these commands using the Bash tool:

### 1. Initialize git (if not already in a repo)

First check if already in a git repo:
```bash
git rev-parse --is-inside-work-tree 2>/dev/null || git init
```

### 2. Install dependencies

```bash
npm install
```

### 3. Initialize Husky

```bash
npx husky init
```

Then create `.husky/pre-commit` using the Write tool (not shell echo) with this content:
```bash
npm run typecheck
npx lint-staged
```

### 4. Verify setup

```bash
npm run check
```

### 5. Create initial commit

```bash
git add .
git commit -m "Initial commit: project scaffold with TypeScript + Vite

- TypeScript with strict mode
- ESLint + Prettier for code quality
- Husky + lint-staged for pre-commit hooks
- Vitest for testing
- Knip for dead code detection
- GitHub Actions CI workflow

Co-Authored-By: Claude <noreply@anthropic.com>"
```

---

## Feedback Loop

1. **Write code** → TypeScript catches type errors immediately
2. **Save file** → Editor shows ESLint warnings
3. **Attempt commit** → Pre-commit runs typecheck + lint-staged
4. **Push to remote** → CI runs full suite including Knip and tests
5. **Periodically** → Run `npm run knip` to find dead code
