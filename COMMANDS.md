# Commands

EasyInvoicePDF is a Next.js app that creates invoice PDFs in the browser. Invoice data stays in the browser; there's no database.
It needs **Node 24** and **pnpm 10.33.4** (older pnpm versions can't read `pnpm-workspace.yaml`). Licence: GNU AGPL v3, see `LICENSE`.

## Setup

```
npm install -g pnpm@10.33.4          # or: corepack enable
pnpm i                               # uses pnpm-lock.yaml and applies the fontkit patch
cp .env.example .env.local           # placeholders: enough to build and run locally
```

Emails, Telegram, Google Drive, Upstash Redis and Sentry aren't reachable with the placeholders. The invoice form and PDFs don't need them.

## Run

```
pnpm dev                             # http://localhost:3000
pnpm build && pnpm start             # production build; another port: PORT=3100 pnpm start
```

## Check everything

```
pnpm check                           # lint, types, format, unit tests
```

One at a time:

```
pnpm lint                            # oxlint
pnpm type-check                      # tsc
pnpm format:check                    # oxfmt --check; to fix: pnpm format
pnpm test                            # vitest, all unit tests
pnpm exec vitest run <path>          # one test file
```

## End-to-end tests (Playwright, against a running app)

```
pnpm exec playwright install chromium             # once
pnpm build && pnpm start                          # in one terminal
pnpm exec playwright test --project="Desktop Chrome"
pnpm exec playwright test e2e/invoice-form.test.ts --project="Desktop Chrome"     # one file
pnpm e2e:not-flaky --project="Desktop Chrome"     # 2 workers, if the machine is slow
```

The saved PDF screenshots in `e2e/**/*-snapshots/` are for macOS (`-darwin.png`). On Linux, Playwright has none to compare with, so those tests write new files and fail on the first run. To make them for this machine:

```
pnpm exec playwright test --project="Desktop Chrome" --update-snapshots
```

Known results on Desktop Chrome: 1 test in `e2e/server-render-pdf/server-render-pdf.test.ts` fails with the placeholders, because that route needs a real Upstash Redis. 4 tests in `e2e/invoice-templates-common/shared-template-features.test.ts` can time out with 4 workers; they pass with `--workers=2`.
