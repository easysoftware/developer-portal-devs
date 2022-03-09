# Backend testing

*In this article we will show you how to test your BE services.*

---

Testing happens on two fronts, automated tests written by coders and user testing by our QA team. It is done before code is merged (automated always, QA if necessary) and always before releases when the entire bulk of changes is tested as a whole.

---

## Automated tests

Testing is done automatically by [GitLab CI](https://docs.gitlab.com/ce/ci/) on every commit pushed. See the pipeline definition in `.gitlab-ci.yml` in the repo.

The test framework is [rspec](https://github.com/rspec/rspec) and [rspec-rails](https://github.com/rspec/rspec-rails). For factories, we use [factory_bot](https://github.com/thoughtbot/factory_bot).

In RYSes, all specs are in the `/spec` folder.

On the platform (devel/devel), tests are in each plugin directory in `test/spec`.

You can run all the tests locally:

1. **In the RYSes**:

```ruby
 # all tests
 bundle exec rspec
 # just particular
 bundle exec rspec spec/services/easy_data_receiver/joomla_recurring_payment_service_spec.rb
```

2. **In the platform**:

```ruby
rake easyproject:tests:spec RAILS_ENV=test
# OR by native rspec, similar to RYSy
bundle exec rspec plugins/easyproject/easy_plugins/easy_extensions/test/spec/models/user_spec.rb
```

---

## Cypress tests

*How to write CY tests? How we manage them and how to run them?*

### Getting started with Cypress
At the beginning please read some basics about Cypress:

1. [Installing Cypress](https://docs.cypress.io/guides/getting-started/installing-cypress) 
2. [Assertions](https://docs.cypress.io/guides/references/assertions)
3. And most importantly - [Best practices](https://docs.cypress.io/guides/references/best-practices). 

### Structure

On the platform, Cypress tests are located in `/cypress/integration` folder. The main configuration of cypress is in `/cypress.json file`.

Cypress itself runs JS tests in its own syntax against some running server - this is specified in `/cypress.json`.

### Database cleaner

Before every test (or suite) you need to call BE database cleaner to clean-up the database. For this, it prepared the command `cy.app ("clean")` which makes the "database clean".

### Factories / Fixtures

Server **MUST** contains data for testing - but it can be created directly for tests.
For the rest of our tests we use [***FactoryBot***](https://github.com/thoughtbot/factory_bot) and all entities have their own factory.

We use integration [***cypress-on-rails***](https://github.com/shakacode/cypress-on-rails) which creates something like a "bridge" between FactoryBot and cypress.

> If you need, for example, to create a user, you just call `cy.FactoryBot(["create", "user"])` after the database cleans and you can use existing users in your tests.

### Scenarios

Usually, complex tests need complex test data. For example, if you want to test how filters are working, you need several different entities for filtering. 

This recommended option is [***Scenario***](https://github.com/shakacode/cypress-on-rails#example-of-using-scenarios) - where you'll create testing data directly through [***FactoryBot***](https://github.com/thoughtbot/factory_bot) - so you can pass relations between entities.

It's in Ruby, but FactoryBot has very common syntax, so we think that everybody can do it.

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
1. Make sure that you have bundle install and assets precompiled
2. Run
```
bundle install
```
3. Run
```
bundle exec rake easyproject:install
```
4. Run your server aside in the test environment and with CYPRESS enable:

```ruby
CYPRESS=1 FORCE_HTTP=1 bundle exec rails s
```

> FORCE_HTTP allows puma accept HTTP request (insecure) in production.

5. Run Cypress
```ruby
cypress run
```
or use docker instead:

```ruby
docker run -it -v $PWD:/e2e -w /e2e cypress/included:3.4.0
```

> At GitlabCI there is a cypress stage for running examples "automatically". On platform
> (devel/devel) is set as a `manual` - so you need to explicitly execute it in your MR.



