
# Agent Guide — Day.js

## Project
Day.js is a lightweight, immutable JavaScript date/time library with a Moment.js-compatible API. Core functionality is kept small; optional plugins and locale modules extend its behavior. Preserve backwards compatibility and avoid unnecessary bundle-size increases.

## Architecture
- `src/index.js`: main Day.js factory, date wrapper, parsing, formatting, and manipulation.
- `src/utils.js` and `src/constant.js`: shared utilities and constants.
- `src/plugin/<name>/index.js`: optional plugins. Plugins export an extension function; consumers activate them with `dayjs.extend(plugin)`.
- `src/locale/`: locale definitions for formatting and localization.
- `test/*.test.js`: core Jest tests; `test/plugin/`, `test/locale/`, and `test/issues/` cover extensions, locales, and regressions.
- `types/`: source TypeScript declarations. `build/` contains Rollup and output-generation scripts, not application source.

## Commands
From the repository root:
- Install: `npm ci`
- Build and size check: `npm run build`
- Standard lint (Unix shells): `npm run lint`
- Standard test matrix (Unix shells): `npm test`
- On Windows, set `$env:TZ = "UTC"` before running any targeted Jest test, e.g. `npx jest test/plugin/dayOfYear.test.js --runInBand --coverage=false`. This provides a reproducible baseline, not full multi-timezone coverage.

On native Windows PowerShell, the standard scripts have portability issues: `npm run lint` uses a Unix-style executable path, while `npm test` invokes Unix `date`. Use:
- `$env:TZ = "UTC"` then `npx jest --runInBand --coverage=false`
- `npx eslint src/* test/* build/* --rule 'linebreak-style: off'` if Git has converted LF endings to CRLF.

These Windows commands are local validation alternatives, not replacements for the full multi-timezone matrix and coverage checks. Tests can behave differently across time zones.

## Conventions
- Follow `.eslintrc.json` (Airbnb base): ES6 syntax, 2-space indentation, no semicolons, no trailing commas.
- Preserve LF line endings as specified by `.editorconfig`. Avoid unrelated reformatting.
- Keep date operations immutable unless a plugin explicitly implements mutability.
- Add regression tests alongside the affected feature. Plugin tests live in `test/plugin/<name>.test.js`; register plugins with `dayjs.extend`.
- Follow nearby Jest `describe`/`it`/`expect` patterns. Use fixed dates for reproducible assertions; compare against Moment.js where existing tests do so.
- Keep the core small. The build enforces a 2.99 KB size limit for `dayjs.min.js`.

## Guardrails
- Edit source under `src/`, tests under `test/`, or source declarations under `types/`; do not hand-edit generated root-level `locale/`, `plugin/`, `esm/`, `dayjs.min.js`, `locale.json`, or `index.d.ts`.
- Root `plugin/` and `locale/` are build outputs; `src/plugin/` and `src/locale/` are their editable sources.
- Do not modify `node_modules/`, generated `coverage/`, or dependencies/lockfiles for unrelated tasks.
- Do not run `npm audit fix --force` to address installation warnings during unrelated work.
- Before diagnosing date-dependent failures as code regressions, verify the `TZ` setting and rerun relevant tests.
