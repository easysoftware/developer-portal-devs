# Backend testing

Testing happens on two fronts, automated tests written by coders and user testing by our QA team. It is done before code
is merged (automated always, QA if necessary) and always before releases when the entire bulk of changes is tested as a
whole. In this section we will focus on automated testing.

---

## Automated tests

Testing is done automatically by [GitLab CI](https://docs.gitlab.com/ce/ci/) on every commit pushed. See the pipeline
definition in `.gitlab-ci.yml` in the repo.

### Getting started

Our test framework is [RSpec](https://github.com/rspec) and [rspec-rails](https://github.com/rspec/rspec-rails).
For factories, we use [FactoryBot](https://github.com/thoughtbot/factory_bot).

1. To be more familiar with RSpec we recommend you to visit [this](https://rspec.info/documentation) page.
2. For best practices visit [this](https://www.betterspecs.org/) page.

#### Controller tests

Be mindful of the different RSpec types and their purpose when writing tests. Avoid testing behaviour of jobs using a
controller test for example. If you need to test a job - use `type: :job` test. Controller tests should only test what
the controller is responsible for, specifically:

* templates rendering (not their content)
* redirects
* instance variables assigned in the controller to be shared with views
* cookies sent back with the response

So if there is a job performed or a service called, controller tests should just test that they are performed or called
with correct parameters, not their implementation.

### Structure

In RYSes, all specs are in the `/spec` folder.

On the platform (devel/devel), tests are in each plugin directory in `test/spec`.

### Running tests locally

- All tests
  ```
  bundle exec rspec
  ```
- Specific test
  ```
  bundle exec rspec PATH/TO/SPECFILE

  # example in RYS
  bundle exec rspec spec/services/easy_data_receiver/joomla_recurring_payment_service_spec.rb

  # example in platform
  bundle exec rspec plugins/easyproject/easy_plugins/easy_extensions/test/spec/models/user_spec.rb
  ```
- Specific example using line number
  ```
  bundle exec rspec plugins/easyproject/easy_plugins/easy_extensions/test/spec/models/user_spec.rb:31
  ```

#### Feature tests in browser

Sometimes you need to run legacy feature tests in browser to see what's really happening and detect hidden problems. For
that you need to have a chrome based browser installed and **chromedriver** working.

###### Mac

- Install chromedriver
  ```
  brew install --cask chromedriver
  ```
- As an "external" app you need to add it to quarantine to run it
  ```
  xattr -d com.apple.quarantine chromedriver
  ```
- _Optionally_ you might need to update your `spec_helper.rb` and add path to your browser binary to capybara chrome
  driver registration:
  ```ruby
  # example for using Brave browser
  browser_options = Selenium::WebDriver::Chrome::Options.new(args: ENV['CHROME_OPTIONS'].to_s.split,
                                                             binary: '/Applications/Brave Browser.app/Contents/MacOS/Brave Browser')
  ```
- Then to run the test use
  ```
  JS_DRIVER=chrome bundle exec rspec PATH/TO/SPECFILE
  ```
