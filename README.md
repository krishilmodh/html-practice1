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

## Demo 1
Set timeout  ||  Annotations  ||  Skip test  ||  Only test
``` bash
import { test, expect } from "@playwright/test";

test("test", async ({ page }) => {
  test.setTimeout(4000);
  await page.goto("https://demo.playwright.dev/todomvc/#/");
  await page.getByPlaceholder("What needs to be done?").click();
  await page
    .getByPlaceholder("What needs to be done?")
    .fill("my name is krishil");
  // await page.waitForTimeout(5000);
  await page.getByPlaceholder("What needs to be done?").press("Enter");
  await page
    .getByPlaceholder("What needs to be done?")
    .fill("i am fullstack developer");
  await page.getByPlaceholder("What needs to be done?").press("Enter");
  await page.getByPlaceholder("What needs to be done?").click();
  await page.getByPlaceholder("What needs to be done?").fill("add new");
  await page.getByPlaceholder("What needs to be done?").press("Enter");
  await expect(page.getByTestId("todo-title")).toHaveCount(3);
  await page
    .locator("li")
    .filter({ hasText: "i am fullstack developer" })
    .getByLabel("Toggle Todo")
    .check();
  await page.getByRole("link", { name: "Active" }).click();
  await expect(page.getByTestId("todo-title")).toHaveCount(2);
  await page.getByRole("link", { name: "Completed" }).click();
  
  
});
//annotaion
test.skip("has title", async ({ page }) => {
  await page.goto("https://playwright.dev/");

  // Expect a title "to contain" a substring.
  await expect(page).toHaveTitle(/Playwright/);
});

// test.only("only title", async ({ page }) => {
//   await page.goto("https://playwright.dev/");

//   // Expect a title "to contain" a substring.
//   await expect(page).toHaveTitle(/Playwright/);
// });
```
## Slow Motion
 
```bash
test("test", async () => {
  const browser = await chromium.launch({
    slowMo: 500,
    headless: false,
  });
  const context = await browser.newContext();
  const page = await context.newPage();

  await page.goto("https://demo.playwright.dev/todomvc/#/");
  // rest of the code
});
```
## Demo 2
### Web component test
HTML link :-[CODE](https://github.com/krishilmodh/html-practice1/blob/main/html.md)
```bash
import { test, expect } from "@playwright/test";
test("test", async ({ page }) => {
  await page.goto('http://127.0.0.1:5500/WebComponents/TODO-LIST/index.html');
  await page.getByRole('textbox').click();
  await page.getByRole('textbox').fill('krishil');
  await page.getByRole('button', { name: 'Add' }).click();
  await page.getByRole('textbox').click();
  await page.getByRole('textbox').fill('deep');
  await page.getByRole('button', { name: 'Add' }).click();

  // Assert that the todos were added
  expect(await page.locator("text=krishil").count()).toBe(1);
  expect(await page.locator("text=deep").count()).toBe(1);
});
```
## Demo 3
Hooks
```bash
import { test, expect } from "@playwright/test"
import { test, expect } from "@playwright/test";
test.describe("navigation", () => {
  test.beforeEach("login", async ({ page }) => {
    await page.goto("https://www.saucedemo.com/v1/");
    await page.locator('[data-test="username"]').click();
    await page.locator('[data-test="username"]').fill("standard_user");
    await page.locator('[data-test="password"]').click();
    await page.locator('[data-test="password"]').fill("secret_sauce");
    await page.getByRole("button", { name: "LOGIN" }).click();
  });
  test("homepage", async ({ page }) => {
    await page
      .locator("div")
      .filter({ hasText: /^\$29\.99ADD TO CART$/ })
      .getByRole("button")
      .click();
    await page
      .locator("div")
      .filter({ hasText: /^\$9\.99ADD TO CART$/ })
      .getByRole("button")
      .click();
    await page.getByRole("link", { name: "Sauce Labs Bolt T-Shirt" }).click();
    await page.getByRole("button", { name: "ADD TO CART" }).click();
    await page.close();
  });

  test("logout", async ({ page }) => {
    await page.getByRole("button", { name: "Open Menu" }).click();
    await page.getByRole("link", { name: "Logout" }).click();
    await page.close();
  });
});
```
## Mock Api
```bash
import { test, expect, chromium } from "@playwright/test";

test("mocks a fruit and doesn't call api", async () => {
  const browser = await chromium.launch({
    slowMo: 1000,
    headless: false,
  });
  const context = await browser.newContext();
  const page = await context.newPage();
  // Mock the api call before navigating
  await page.route("*/**/api/v1/fruits", async (route) => {
    const json = [{ name: "Strawberry", id: 21 }];
    await route.fulfill({ json });
  });
  // Go to the page
  await page.goto("https://demo.playwright.dev/api-mocking");

  // Assert that the Strawberry fruit is visible
  await expect(page.getByText("Strawberry")).toBeVisible();
});
test("gets the json from api and adds a new fruit", async () => {
  const browser = await chromium.launch({
    slowMo: 2000,
    headless: false,
  });
  const context = await browser.newContext();
  const page = await context.newPage();
  // Get the response and add to it
  await page.route("*/**/api/v1/fruits", async (route) => {
    const response = await route.fetch();
    const json = await response.json();
    json.push({ name: "Loquat", id: 100 });
    // Fulfill using the original response, while patching the response body
    // with the given JSON object.
    await route.fulfill({ response, json });
  });

  // Go to the page
  await page.goto("https://demo.playwright.dev/api-mocking");

  // Assert that the new fruit is visible
  await expect(page.getByText("Loquat", { exact: true })).toBeVisible();
});
```
## Demo 4
```bash
import { test, expect } from '@playwright/test';

test('test', async ({ page }) => {
  await page.goto('https://cloudtesting.contosotraders.com/');
  await page.getByPlaceholder('Search by product name or').click();
  await page.getByPlaceholder('Search by product name or').fill('xbox');
  await page.getByPlaceholder('Search by product name or').press('Enter');
  await page.getByRole('img', { name: 'Xbox Wireless Controller Mineral Camo Special Edition' }).click();
  await expect(page.locator('div').filter({ hasText: /^Xbox Wireless Controller Mineral Camo Special Edition$/ })).toBeVisible();
  await expect(page.locator('div').filter({ hasText: /^Quantity-\+$/ }).getByRole('textbox')).toHaveValue('1');
  await page.getByRole('button', { name: '+' }).click();
  await expect(page.locator('div').filter({ hasText: /^Quantity-\+$/ }).getByRole('textbox')).toHaveValue('2');
  await page.getByRole('button', { name: 'Add To Bag' }).click();
  await expect(page.getByLabel('cart')).toContainText('1');
  await page.getByLabel('cart').click();
  await expect(page.getByText('Xbox Wireless Controller')).toBeVisible();
  await page.getByRole('link', { name: 'Remove' }).click();
  await expect(page.getByRole('heading', { name: 'Your Cart is empty' })).toBeVisible();
  
});
```

