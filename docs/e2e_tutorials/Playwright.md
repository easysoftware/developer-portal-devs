# Playwright tests

We are using Playwright for testing our Frontend.
In this section we will try to answer few questions, for example:
*How to write Playwright tests? How we manage them and how to run them?*

## Getting started with Playwright
At the beginning please read some basics about Playwright:

1. [Installing Playwright](https://playwright.dev/docs/intro#installing-playwright)
2. [Assertions](https://playwright.dev/docs/test-assertions)
3. And most importantly - [Best practices](https://playwright.dev/docs/selectors#best-practices).

## Structure

On the platform, Playwright tests are located in `/playwright` folder. The main configuration of Playwright is in `/playwright.config.ts` file.

Playwright itself runs JS tests in its own syntax against some running server - this is specified in `/playwright.config.ts`.

1. ./playwright/fixtures

- This folder contains JSON files representing entities in our system. If a JSON contains an ID the entity exists in the testing database, otherwise they're mostly used for filling inputs.

2. ./playwright/pages

- For creating playwright test we use “Page pattern”. That means the logic of finding elements in a page is enclosed in files with `.page`. These are imported into the test and their methods are called.

3. ./playwright/tests

- This folder contains Playwright tests with defined expectations.

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

7. Run Playwright
```
yarn playwright test
```

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
