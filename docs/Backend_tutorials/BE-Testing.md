# Backend testing

*In this article we will show you how to test your BE services.*

---

Testing happens on two fronts, automated tests written by coders and user testing by our QA team. It is done before code is merged (automated always, QA if necessary) and always before releases when the entire bulk of changes is tested as a whole. In this section we will focus on automated testing.

---

## Automated tests

Testing is done automatically by [GitLab CI](https://docs.gitlab.com/ce/ci/) on every commit pushed. See the pipeline definition in `.gitlab-ci.yml` in the repo.


### Getting started

Our test framework is [RSpec](https://github.com/rspec/rspec) and [rspec-rails](https://github.com/rspec/rspec-rails).
For factories, we use [factory_bot](https://github.com/thoughtbot/factory_bot).

1. To be more familiar with RSpec we recommend you to visit [this](https://relishapp.com/rspec/) page.
2. For best practices visit [this](https://www.betterspecs.org/) page.

### Structure

In RYSes, all specs are in the `/spec` folder.

On the platform (devel/devel), tests are in each plugin directory in `test/spec`.

### How to run tests locally

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
