# Frontend testing

*In this article we will show you how to test your FE services.*

---



## Frontend tests

We are using [Jest](https://jestjs.io/), for both platform and plugins (mainly Vue.js)

You can run them from the source root as:

```ruby
npm test

# or just particular
npm test -- query.spec.js    

```

> There are old tests written in ***Jasmine***. Look for file `jasmine_spec.rb` in the platform repository. They run through Rspec inside the GUI for the customer.
>
> For example, rspec with specific params for controller:`plugins/easy_scheduler/test/spec/features/jasmine_spec.rb`. 
>
> In the view there is condition for that specific param, which includes jasmine scaffold:
> `plugins/easy_scheduler/app/views/easy_scheduler/index.html.erb:85`.
>
> Here's the jasmine scaffolding and definition of tests:
> `plugins/easy_scheduler/app/views/easy_scheduler/_test_includes.html.erb`.
>
>Finally, here's one of the 'pure' jasmine tests:
>`plugins/easy_scheduler/assets/javascripts/jasmine/users.js`.

---

## Playwright tests

We are using Playwright for testing our Frontend.
In this section we will try to answer few questions, for example:
*How to write Playwright tests? How we manage them and how to run them?*

### Getting started with Playwright
At the beginning please read some basics about Playwright:

1. [Installing Playwright](https://playwright.dev/docs/intro#installing-playwright)
2. [Assertions](https://playwright.dev/docs/test-assertions)
3. And most importantly - [Best practices](https://playwright.dev/docs/selectors#best-practices).

### Structure

On the platform, Playwright tests are located in `/playwright` folder. The main configuration of Playwright is in `/playwright.config.ts` file.

Playwright itself runs JS tests in its own syntax against some running server - this is specified in `/playwright.config.ts`.

1. ./playwright/fixtures

- This folder contains JSON files representing entities of our system. Both existing once and the new ones.

2. ./playwright/pages

- For creating playwright test we use “Page pattern”. That means the logic of finding elements in a page is enclosed in files with `.page`. These are imported into the test and their methods are called.

3. ./playwright/tests

- This folder contains Playwright tests with defined expectations.

### Selectors - using `data-cy`

First please read [this guide](https://docs.cypress.io/guides/references/best-practices) and follow these hints:

1. Targeting elements by css selectors such as: `id` and `class`, is allowed only in combination with `data-cy` attribute.

2. Avoid targeting elements based on HTML tags such as: `div`, `img` or `a`, unless it's in combination with a `data-cy` attribute.

3. Avoid complex selectors as `div.mydiv > ul > li > a.target`.

4. Never assert context based on localized strings.

5. Use own `data-cy` attribute in away that is specific and semantic.

6. Preferably use `subject__action--target` pattern, i.e. `button__open--crm-index`.

<!-- theme: danger -->
>This is valid for views tests, or every other test which expect some CSS. Avoid CSS, use custom `data-cy` attribute

### How to write them?

If HTML is rendered by backend you need to pass `"data-cy": "some-value"`, for example:

```ruby
link_to(l(:label), url, class: 'customize-button', "data-cy": "button__customize_page")
# OR
link_to(l(:label), url, class: 'customize-button', data: { cy: "button__customize_page" })
```
<!-- theme: warning -->
>This is only valid type of `data-cy` = in every other case it is forbidden due to consistency!

### How to run tests locally

Lets see something about Playwright.

You need to run application server aside and run against it Playwright.

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

4. For testing, we use database from [https://cypresssource-minor.easysoftware.com](https://cypresssource-minor.easysoftware.com) .
   **It is forbidden to edit it without permission from QA!** We just simply dump the database and import dump into new database.

5. Run
```
bundle exec rake easyproject:install
```

6. Run your server aside in the test environment:
```ruby
bundle exec rails s
```

> FORCE_HTTP allows puma accept HTTP request (insecure) in production.

7. Run Playwright
```ruby
yarn playwright test
```

## Connection to GitlabCI – internal usage

> At GitlabCI there is a playwright stage for running examples "automatically".

To execute a playwright test in GitlabCI we use docker image. For each branch, new docker image is being created, which is saved in Gitlab registers: https://git.easy.cz/devel/devel/container_registry.

The name of the image contains the name of the branch for which it was created.

Each failed pipeline provides an option to download detailed back-trace zip file in 'Job artifacts' section. You can execute this zip file after downloading it with `yarn playwright show-trace` command. 
