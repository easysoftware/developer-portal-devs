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

## Cypress tests

We are using Cypress for testing our Frontend.
In this section we will try to answer few questions, for example:
*How to write CY tests? How we manage them and how to run them?*

### Getting started with Cypress
At the beginning please read some basics about Cypress:

1. [Installing Cypress](https://docs.cypress.io/guides/getting-started/installing-cypress)
2. [Assertions](https://docs.cypress.io/guides/references/assertions)
3. And most importantly - [Best practices](https://docs.cypress.io/guides/references/best-practices).

### Structure

On the platform, Cypress tests are located in `/cypress/integration` folder. The main configuration of cypress is in `/cypress.json file`.

Cypress itself runs JS tests in its own syntax against some running server - this is specified in `/cypress.json`.

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
link_to(l(:label), url, class: 'customize-button', "data-cy": "button-customize_page")
# OR
link_to(l(:label), url, class: 'customize-button', data: { cy: "button-customize_page" })
```
<!-- theme: warning -->
>This is only valid type of `data-cy` = in every other case it is forbidden due to consistency!

### How to run tests locally

Lets see something about Cypress and local docker.

You need to run application server aside and run against it cypress.

How to do that:
Make sure that you have bundle install and assets precompiled
1. Run
```
bundle install
```
2. Run
```
bundle exec rake easyproject:install
```
3. Run your server aside in the test environment and with CYPRESS enable:

```ruby
CYPRESS=1 FORCE_HTTP=1 bundle exec rails s
```

> FORCE_HTTP allows puma accept HTTP request (insecure) in production.

4. Run Cypress
```ruby
cypress run
```
or use docker instead:

```ruby
docker run -it -v $PWD:/e2e -w /e2e cypress/included:3.4.0
```

> At GitlabCI there is a cypress stage for running examples "automatically". On platform
> (devel/devel) is set as a `manual` - so you need to explicitly execute it in your MR.
