# Repository Guidelines

## Project Structure & Module Organization
The core Next.js app lives in `src/app`, with each route segment defined by a `page.tsx` (and optional `layout.tsx`). Shared UI should move into future `src/app/(components)` or `src/app/(features)` groups, while static assets belong in `public/` and are referenced as `/asset-name.png`. Config roots such as `next.config.ts`, `tsconfig.json`, and `eslint.config.mjs` define aliases like `@/*`; prefer updating these centrally rather than per file. Place any new tests beside their sources using the `*.test.ts` or `*.test.tsx` pattern.

## Build, Test, and Development Commands
Use `pnpm install` to sync dependencies; the lockfile is authoritative. Run `pnpm dev` for a Turbopack dev server at http://localhost:3000. `pnpm build` compiles the production bundle and surfaces type or lint issues, while `pnpm start` serves the built output. ESLint is available through `pnpm lint` and should pass cleanly before opening a pull request.

## Coding Style & Naming Conventions
Write TypeScript React function components with 2-space indentation and double-quoted strings to match the existing codebase. Favor Tailwind utilities for styling (e.g., `className="grid gap-8"`), keeping related utilities grouped for readability. Import shared modules via the `@/` alias instead of long relative paths, and colocate helpers with the route segment when practical.

## Testing Guidelines
Automated testing is not yet wired in. When adding coverage, follow the `*.test.ts(x)` naming convention and prefer React Testing Library for components. Document any manual QA steps in pull requests until a `pnpm test` script is introduced.

## Commit & Pull Request Guidelines
Commit messages use short, present-tense subjects such as `Add hero section layout`. Group related changes together and reference issues in the body when relevant. Pull requests should explain what changed, why it matters, and how to validate (e.g., “Run `pnpm build`”). Include screenshots for UI updates and call out known gaps so reviewers can focus their attention.

## Security & Configuration Tips
Store secrets in `.env.local` and document required keys when introducing them. Gate experimental Next.js settings in `next.config.ts`, adding concise inline comments if behavior deviates from the defaults. Avoid committing generated secrets or credentialed data.
