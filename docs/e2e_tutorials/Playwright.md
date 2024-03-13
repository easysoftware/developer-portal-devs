# Playwright tests

We are utilizing Playwright for testing our Frontend. In this section,
we will address several key questions, including:

- How to write Playwright tests?
- How do we manage them?
- How do we run them?*

## What is Playwright?
Before diving into writing tests with Playwright,
it's essential to familiarize yourself with the basics of the tool.
Here's a primer to help you get started:

Playwright is an open-source automation tool developed by Microsoft that allows you to automate interactions with web browsers.
It provides a high-level API for automating browsers such as Chromium, Firefox, and WebKit,
enabling developers to write reliable and maintainable end-to-end tests for web applications.

## Important Links for Getting Started with Playwright

To kickstart your journey with Playwright,
here are a few essential links to help you navigate through the installation process,
understand assertions, and adopt best practices:

1. [Installing Playwright](https://playwright.dev/docs/intro#installing-playwright): Learn how to install Playwright in your project and set up the necessary dependencies to begin writing tests.
2. [Assertions in Playwright](https://playwright.dev/docs/test-assertions): Explore the built-in assertion methods provided by Playwright to validate the behavior of your application during test execution
3. [Best Practices in Playwright](https://playwright.dev/docs/best-practices#best-practices): Familiarize yourself with the recommended best practices for writing maintainable and efficient Playwright tests, ensuring the reliability and scalability of your testing efforts.

## Structure

In our project, Playwright tests are organized within the /playwright folder. The main configuration for Playwright resides in the /playwright.config.ts file.
To enhance our testing structure and workflow, we've made significant updates:

**Separating Playwright from Core**:
- **Overview**: Playwright functionality has been separated from the Core of our application, improving organization and maintenance.
- **Changes Made**:
   - Playwright functionality has been relocated to its folder within the Core repository.
   - The Playwright folder now manages its dependencies independently with its package.json file.

### File Structure

1. /playwright: This directory contains all Playwright test files and related resources.

2. /playwright.config.ts: The main configuration file for Playwright. Here, you can specify settings such as browsers to use, test environment setup, and other global configurations.

**example**
```ts
// playwright.config.ts

import { PlaywrightTestConfig } from '@playwright/test';

const config: PlaywrightTestConfig = {
// Specify the test environment configuration
use: {
// Specify the browser contexts to be used for tests
// Example: chromium, firefox, webkit
// browsers: ['chromium'],

    // Specify the base URL for the server under test
    // baseURL: 'http://localhost:3000',
},
};

export default config;
```
#### Notes:
- In this example configuration, you can uncomment and adjust the browsers array to specify which browsers to use for testing. However, for your testing, we only use the Chrome browser. Do not use any other browser without discussing it with your lead or manager.
- For the baseURL: If you don't have the possibility to run the application locally, use testing applications.
  Replace 'http://localhost:3000' with the URL of your testing application when you are working with the tests.

**Important:** Never add and push changes from playwright.config.ts without discussing them with your lead or manager. These changes are intended for local testing purposes only and should not be pushed to the repository without approval.

3. ./playwright/constants
   This folder contains main constants that are used exclusively within testing files.

**example:**
```ts
export const CURSOR_POSITION_DEFAULT = 5;
```

4. ./playwright/fixtures

- This folder contains JSON files representing entities in our system.
- If a JSON file contains an ID, it indicates that the corresponding entity exists in the testing database. Otherwise, these JSON files are primarily used for filling inputs.

**example**
```json
{
  "name": "admin",
   "id": 1
}
```

5. ./playwright/helpers
- This folder contains helper methods that can be utilized from any file within the project.

**example:**
```ts
  async navigateToProjectById(id: string) {
    await this.navigateToUrl(`projects/${id}`);
  }
```

6. ./playwright/pages

- For our tests in Playwright, we adhere to the Page Object Pattern. This methodology centralizes the logic for identifying and interacting with elements within files named with a .page extension.

- These .page files serve as integral components of the Page Object Model (POM), housing methods, and elements essential for test automation.
  They encompass functionality for interacting with various components of the application, including pages, modals pop-ups, forms, and other elements pertinent to the pages under test.

**Also have two important pages within our project:**

1. base.page: This page serves as a central repository for essential methods that can be utilized across various other pages, test files, and layouts.

2. layout page: Here, we gather methods and elements that are accessible from different pages within our project.

**example:**
```ts
import { expect, Page } from "@playwright/test";

export class BasePage {
    readonly page: Page;
    readonly submitBtn: string | null;

    constructor(page: Page, submitBtn: string | null = null) {
        this.page = page;
        this.submitBtn = submitBtn;
    }
}
```

7. ./playwright/plugins

- This folder contains additional methods specifically designed for use in test files. These methods are not directly connected to the tests but serve as utilities to aid in test development and execution.

**example:**
```ts
export const globalBeforeEach = async ({}, testInfo: TestInfo) => {
   console.log(testInfo.title);
};
```

8. ./playwright/tests

- This folder contains Playwright tests with defined expectations.

### Test File Structure

**Each test file should adhere to the following structure:**

1. test.describe: This method should provide a concise description of the purpose of the test file and the types of tests included therein.

2. test.beforeAll() or test.beforeEach(): Use these methods to perform actions that need to be executed before all tests or before each individual test, respectively.

3. test() with a Short Description: Each test should be wrapped within a test() function, accompanied by a brief description outlining what the test is about.

4. test.afterAll() or test.afterEach(): Utilize these methods to execute actions after all tests or after each individual test, respectively.

**example**
```ts
import { test, expect, Page } from "@playwright/test";

test.describe.serial("Create and work with the projects", () => {
  test.beforeAll(async ({ browser }) => {
    const pageContext = await browser.newContext();
    page = await pageContext.newPage();
    await autologin(page);
  });

  test.beforeEach(globalBeforeEach);

  test("Create a new project: #task id", async () => {
        //Your code 
  });


  test.afterAll(async () => {
      await page.close();
   });
```

9. ./playwright/types
- This folder houses enumeration types.

**example**
```ts
export enum ActivityType {
  Office = "Office",
  Vacation = "Vacation",
}
```
10. ./playwright/utils

- This folder contains both global and specific methods that include programming logic not directly tied to Playwright functionalities.

**example**
```ts
export const getDateInUnifiedFormat = (inputDate: string): string => {}
```

## Selectors - using `data-cy`

First please read [this guide](https://playwright.dev/docs/best-practices) and follow these hints:

1. Targeting elements by css selectors such as: `id` and `class`, is allowed only in combination with `data-cy` attribute.

2. Avoid targeting elements based on HTML tags such as: `div`, `img` or `a`, unless it's in combination with a `data-cy` attribute.

3. Avoid complex selectors as `div.mydiv > ul > li > a.target`.

4. Never assert context based on localized strings.

5. Use own `data-cy` attribute in away that is specific and semantic.

6. Preferably use `subject__action--target` pattern, i.e. `button__open--crm-index`.

<!-- theme: danger -->
>This is valid for views tests, or every other test which expect some CSS. Avoid CSS, use custom `data-cy` attribute

## How to write them?

If HTML is rendered by backend you need to pass `"data-cy": "some-value"`, for example:

```ruby
link_to(l(:label), url, class: 'customize-button', "data-cy": "button__customize_page")
# OR
link_to(l(:label), url, class: 'customize-button', data: { cy: "button__customize_page" })
```
<!-- theme: warning -->
>This is only valid type of `data-cy` = in every other case it is forbidden due to consistency!

## How to run tests locally

Lets see something about Playwright.

You need to run an application server aside and run Playwright against it.

How to do that:
Make sure that you have bundle install and assets precompiled
1. Run
```
bundle install
```

2. Define new database for playwright. In the file `./config/database.yml`
   example:
```
development:
  adapter: mysql2
  encoding: utf8mb4
  database: playwright_test
  pool: 5
  username: playwright
  password:
```

3. Run
```
rails db:create
```

4. For testing, we use database from [https://e2e-testing-source.easysoftware.com](https://e2e-testing-source.easysoftware.com) .
   **It is forbidden to edit it without permission from QA!** We just simply dump the database and import dump into new database.

5. Run
```
bundle exec rake easyproject:install
```

6. Run your server aside in the test environment:
```
bundle exec rails s
```

> FORCE_HTTP allows puma accept HTTP request (insecure) in production.

7. Navigate to the Playwright folder within the Core repository.
```
cd playwright
```

8. Install dependencies using yarn.
```
yarn install
```

9. Run Playwright
```
yarn playwright test
```

## Test Modification Standards and Workflows
1. When working with the tests, it's essential to create or utilize an existing `easy project` task to track changes, such as creating new tests, fixes, improvements, or refactoring efforts.

2. Start by creating a branch following the naming convention `testautomation/task_id-short-description`. This format is exclusive to QA and should not include any code related to the application except for adding or changing the `data-cy` attribute.

**example**
```text
testautomation/001-created-a-new-project-test
```
3. In the test files, remember to include a test description ending with the `task ID`. This provides crucial context for the changes made, especially when searching for specific tests.

**example**
```ts
  test("Created a project #002", async () => {};
```
4. Maintain a maximum file length of `250` lines for any files used in the tests. If your changes exceed this limit, consider creating a new file and implementing your changes there.

5. When committing changes to `Git`, include the `task ID` as a reference along with a brief description of the modifications made in the current branch. This helps track which tasks prompted specific changes and aids in project management.

**example**
```text
git commit -m "refs #001: #002 Created a new project"
```

6. Upon submitting your Merge Request (MR) for code review, ensure that it receives approval from two reviewers before merging. Your lead or manager will provide the specific platform for posting your MR.


## Recommendation for writing tests

- After reload/redirect/open new page use `waitForNavigation` function, that is defined in playwright/pages/base.page.ts. It waits for the page to be loaded and ready for interaction. For example:

```ts
async navigateToUrl(url: string) {
   await this.page.goto(url);
   await this.page.waitForURL(url);
   await this.waitForDocumentReady();
}
```

- If you need use [waitForResponse](https://playwright.dev/docs/api/class-page#page-wait-for-response)  method, use it inside Promise.all with action that trigger this request. For example:

```ts
async clickOnSaveButton() {
   await Promise.all([
      this.page.waitForResponse("**/easy_wbs.json?*"),
      this.clickOnButton("submit--mindmup"),
   ]);
}
```

- Don't use innerText to test text in an element. Use the [toHaveText](https://playwright.dev/docs/api/class-locatorassertions#locator-assertions-to-have-text) assertion instead. Because innerText returns the current text of the element, but toHaveText waits for an element that has text. InnerText can cause a problem when dynamically rerender the content of the element.

- bad:
```ts
expect(await this.page.locator("[data-cy='button__submit']").innerText()).toBe("Submit");
```

good:
```ts
await expect(this.page.locator("[data-cy='button__submit']")).toHaveText("Submit");
```

- If you are running playwright tests on a deployment, you can use the `throttleNetwork` method from playwright/pages/base.page.ts. It slows down your network speed so you can make sure your tests are stable. But eventually remove it when the tests pass. For example:

```ts
test.beforeAll(async ({ browser }) => {
   const pageContext = await browser.newContext();
   page = await pageContext.newPage();

   adminPage = new AdminPage(page);
   layoutPage = new LayoutPage(page);

   // Remove next line, before merge it into next/... branches
   adminPage.throttleNetwork(page);

   await autologin(page);
});
```
