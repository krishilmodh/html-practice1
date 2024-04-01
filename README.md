# Playwright

Playwright is a powerful, open-source solution for automating web interactions across all major browsers (Chrome, Firefox, Safari, Edge) using a single API. It offers:

- Cross-platform support
- Modern and fast
- Easy to use
- Catches bugs early

## Commands

To run the test generator:

```bash
npx playwright codegen demo.playwright.dev/todomvc

To run tests:
npx playwright test
npx playwright test --ui
npx playwright test --project chromium
npx playwright test --debug
npx playwright test example.spec.ts:10 --debug
npx playwright show-report
npx playwright test --workers 4
npx playwright test --trace on
npx playwright show-report
npx playwright test --reporter=line
npx playwright test --retries=3
```
Annotations
To skip a test:
```bash
test.skip('skip this test', async ({ page }) => {
  // This test is not run
});
```
To focus on a specific test:
```bash
test.only('focus this test', async ({ page }) => {
  // Run only focused tests in the entire project.
});
```
