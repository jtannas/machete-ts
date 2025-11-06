# machete-ts

The tool for hacking your way through JavaScript tech debt.

## Status

Fleshing out the idea; no implementation done

## Concept

A tool for typescript apps (TUI?) that wraps around a bunch of other tools (e.g. `git`+`knip`+`biome`+???) that automates the process of project clean-up.

Is not necessarily opinionated on which tools to use but will start out that way to limit scope.

- `git` for source control
- Github Actions for PR checking
- `cspell` for spellchecking
- `knip` for removing dead code
- `biome` for linting + formatting

## Sample usage

This is how I imagine a user would interact with it.

```
$ npx machete init

NEXT STEP: Analyzing project configuration...
     Source control:     git
     CI provider:        ~none~
     Language:           JavaScript
     Runtime:            Node14
     Runtime Manager:    ~none~
     Package Manager:    npm
     Framework:          create-react-app
     Unit testing:       jest
     E2E testing:        ~none~
     Linter:             eslint
     Formatter:          prettier
     Spellchecker:       ~none~
     Dead code removal:  ~none~
     Dependency Updater: ~none~

NEXT STEP:  Creating safe restore point
✓ Asserted no uncommitted changes are present
Would you like to create a new branch for changes? (Y/n): Y
Creating new branch for changes (machete-cleanup):
-- `git checkout -b machete-cleanup`

NEXT STEP: Collecting desired configuration...
✗ No configuration file detected. Collecting choices now.

> Git is the only supported source control.
> Please select your desired CI provider:
    (x) GitHub Actions
    ( ) CircleCI
    ( ) ~none~
> Would you like to upgrade to TypeScript? (Y/n): Y
> Please select your desired runtime:
    ( ) bun
    (x) node: latest LTS (24)
    ( ) node: oldest maintained LTS (20)
    ( ) node: latest
    ( ) node: specify version
    ( ) ~no change~
> Please select your desired runtime manager:
    (x) mise *recommended for being a polyglot runtime manager
    ( ) asdf
    ( ) nvm
    ( ) ~none~
> Please select your desired package manager:
    ( ) bun
    (x) pnpm
    ( ) yarn
    ( ) npm
> Please select your desired framework:
    (x) vite (react) *recommended upgrade path for create-react-app*
    ( ) tanstack start
    ( ) ~unchanged~
> Please select your desired unit testing tool:
    (x) vitest *recommended since you are upgrading to vite*
    ( ) jest
> Please select your desired E2E testing tool:
    (x) Playwright
    ( ) Cypress
    ( ) ~none~
> Please select your desired linter:
    (x) biome
    ( ) esLint
    ( ) oxlint
    ( ) ~none~
> Please select your desired formatter:
    (x) biome *recommended since you chose biome as your linter*
    ( ) prettier
    ( ) ~none~
> Would you like to add `cspell` for spellchecking? (Y/n): Y
> Would you like to add `knip` for dead code removal (Y/n): Y
> Please select your desired dependency updater:
    (x) renovate
    ( ) dependabot
    ( ) ~none~

Saving desired project configuration to `.machete.jsonc`...
Committing changes...
-- `git commit -am "machete: create configuration file"

NEXT STEP: Adding `machete:check`` script to `package.json`...
> script: npm run prettier . --check && npm run eslint && npm run jest --silent
Script added!
Running check script to verify changes...
-- `npx run machete:check`

CHECK SCRIPT FAILED
Failures logged to `.machete.log`
Please ensure `npx run machete:check` succeeds and then run `npx machete resume` to continue
```

```
$ npx machete resume
✓ Asserted no uncommitted changes are present
✓ Asserted configuration file present
RESUME STEP: `Adding `machete:check` script to `package.json`
✓ `machete:check` script found in `package.json`
✓ Asserted `machete:check` script passes
Committing...
-- `git commit -am "machete: add `machete:check` script to package.json"

NEXT STEP: Adding `machete:fix`` script to `package.json`...
Would you like to add a script for machete to run that attempts to fix issues? (Y/n):
> script: npm run prettier . --write && npm run eslint --fix
Script added!
✓ Asserted `machete:check` script passes
-- `git commit -am "machete: add `machete:fix` script to package.json"

ANALYSIS: Determining next upgrade step...
Estimating feasibility, value, effort, and consequences of mistakes...
    git:              done
    GitHub Actions:   ready; high value; low effort; low risk
    TypeScript:       ready; high value; high effort; low risk
    mise:             ready; high value; medium effort; low risk
    Node24:           ready; high value; medium effort; low risk
    pnpm:             blocked by create-react-app
    vite:             requires Node20+
    vitest:           requires vite
    Playwright:       ready; high value; high effort; low risk
    biome lint:       blocked by create-react-app
    biome format:     blocked by create-react-app
    cspell:           requires Node18+
    knip:             requires Node18+
    renovate:         ready; medium value; medium effort; medium risk
Recommended next step: GitHub Actions
Proceed? (Y/n/<override step>): Y

NEXT STEP: Github Actions
Please select which autofixes you want applied to your PRs:
    [ ] linter_fix
    [x] formatter_check
Please select which available checks you want run on your PRs:
    [x] linter_check
    [x] formatter_check

Creating `.github/workflows` directory...
Adding `pull_requests.yml` workflow skeleton
Adding `formatter_fix` job to `pull_requests.yml`
Adding `linter_check` job to `pull_requests.yml`
Adding `formatter_check` job to `pull_requests.yml
✓ Asserted `machete:check` script passes
-- `git commit -am "machete: add GitHub Actions config for pull requests"

ANALYSIS: Determining next upgrade step...
Estimating readiness, feasibility, value, effort, and consequences of mistakes...
    git:              done
    GitHub Actions:   done
    TypeScript:       ready; high value; high effort; low risk
    mise:             ready; high value; medium effort; low risk
    Node24:           ready; high value; medium effort; low risk
    pnpm:             blocked by create-react-app
    vite:             requires Node20+
    vitest:           requires vite
    Playwright:       ready; high value; high effort; low risk
    biome lint:       blocked by create-react-app
    biome format:     blocked by create-react-app
    cspell:           requires Node18+
    knip:             requires Node18+
    renovate:         ready; medium value; medium effort; medium risk
Recommended next step: Node24
Proceed? (Y/n/<override step>): Y

NEXT STEP: mise
✗ Assert `mise` is installed

Please go to https://mise.jdx.dev/getting-started.html and follow the instructions there to install mise.
Would you like to open a window there now? (Y/n): Y
-- xdg-open https://mise.jdx.dev/getting-started.html

Please ensure `mise version` succeeds and then run `npx machete resume` to continue
```