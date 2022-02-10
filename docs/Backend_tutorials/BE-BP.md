# Backend best practices

*In this article we will show you some of our best practices...*

---

## Changing multiple plugins

This section is about a situation when you need to change multiple plugins from multiple repositories (currently no project is using git submodules).

At the same time, these plugins can be copied/attached to your repository. However, they are just copies, the originals are somewhere else.

This page describes the manual way of how to do this. You can also use EasyCLI to make this easier, but it's important to know how it works.

For example:

You want the platform with:

1. Redmine plugin `easy_gantt`
2. Easy plugin `easy_resource`
3. RYS plugin `easy_wbs`

First, you must clone everything:

```ruby
# Platform
git clone PLATFORM apps/platform

# Plugins
git clone EASY_GANTT external_plugins/easy_gantt
git clone EASY_RESOURCE external_plugins/easy_resource
git clone EASY_WBS external_plugins/easy_wbs

```

### Redmine or Easy plugins

1. Remove old plugin:

```ruby
rm -rf APP_ROOT/plugins/easy_gantt
rm -rf APP_ROOT/plugins/easyproject/easy_plugins/easy_resource

```

2. Link the new ones:

```ruby
ln -s external_plugins/easy_gantt APP_ROOT/plugins
ln -s external_plugins/easy_resource APP_ROOT/plugins/easyproject/easy_plugins

```
3. Remove from git status (optional):

```ruby
# APP_ROOT/.git/info/exclude

APP_ROOT/plugins/easy_gantt
APP_ROOT/plugins/easyproject/easy_plugins/easy_resource
```

### RYS plugins

Since a RYS is a gem and gem dependencies are managed by a bundler, all you need to do is add it to your `Gemfile.local` using a `path` option or:

```ruby
bundle config local.easy_wbs external_plugins/easy_wbs
bundle install

```

---

## Timezones

Timezones (or just `TZ`) appear in 3 different places.

1. In the database (*eg how the value is stored*).
2. In the user's profile in Redmine (*eg his preference of interpretation*).
3. In his environment (*workstation, browser etc*). This is also sometimes called 'local offset'.

The chosen and preferred way for Redmine (and so for EP and ER too) is/should be

1. Store values in database in UTC.
2. Expose dates in API at UTC.
3. Expose dates in generated HTML in user's Redmine TZ settings.
4. Expect input including timezone (or interpret them as in local user offset).


<!-- theme: warning -->
>This description is unfortunately just the definition of our vision, not the current/ current state.

### Current problems

1. How the Redmine works with timezones (*see above*).
2. Not all endpoints behave as expected, so sometimes the FE gets value with TZ, sometimes UTC, sometimes without zone.
3. The JavaScript date object always displays in the local timezone (*eg timezone set by the location of the browser*), see official documentation or this explanatory article.
4. A user can set a specific timezone in his profile in ER/EP.
5. Timezone set in Redmine can be different than his browser/workstation has

### Our solutions

At the frontend, use `EASY.utils.parseDate` to wrap the actual value with:

1. Decides about format.
2. Normalizes it (*eg deal with local timezone*).
3. Convert to the desired timezone.
4. Parse into JavaScript date.

<!-- theme: info -->
>Because the implementation uses local offset, which is peculiar to set in JavaScript tests, we've set Jest to use 2 different timezones:
>
>**UTC** - used by default, when you run `npm test`
>
>**TZ+-5** - used, when you run `npm run test-all`.  That will execute only tests defined as `test_utc_plus5` (or `test_utc_minus5`). *Other tests in the same file will be **skipped***.

### Examples

#### `test_file_A.js` - usage of TZ tests

```ruby
import {test_utc_plus5, test_utc_minus5} from "../timezone_setup";

describe("test A", () => {
 test('got executed under UTC', () => {
  // foo
 });

 test_utc_minus5("got executed under -5", () => {
  // boo
 });

 test_utc_plus5("got executed under -5", () => {
  // loo
 });
});

```

#### `test_file_B.js `- just regular test

```ruby
describe("test B", () => {
 test('got executed under any TZ', () => {
   // zoo
 });

});

```

**and now execute them:**

#### `npm run test` will execute everything under UTC:

```ruby
 $ npm run test
 > TZ=UTC jest
 ..
 
 PASS  test/jest/tests/test_file_B.test.js
 test B
 ✓ got executed under any TZ

 PASS  test/jest/tests/test_file_A.test.js
 test A
 ✓ got executed under UTC (1 ms)
 ○ skipped got executed under +5
 ○ skipped got executed under -5

```

#### `npm run test-all` will run tests under 3 different timezones:

```ruby
$ npm run test-all
..
> TZ=UTC jest
..
 PASS  test/jest/tests/test_file_B.test.js
  test B
    ✓ got executed under any TZ

 PASS  test/jest/tests/test_file_A.test.js
  test A
    ✓ got executed under UTC
    ○ skipped got executed under +5
    ○ skipped got executed under -5

..
> TZ=America/Cancun jest
..
 PASS  test/jest/tests/test_file_B.test.js
  test B
    ✓ got executed under any TZ

 PASS  test/jest/tests/test_file_A.test.js
  test A
    ✓ got executed under +5
    ○ skipped got executed under UTC
    ○ skipped got executed under -5

..
> TZ=Indian/Maldives jest
..
 PASS  test/jest/tests/test_file_B.test.js
  test B
    ✓ got executed under any TZ (1 ms)

 PASS  test/jest/tests/test_file_A.test.js
  test A
    ✓ got executed under -5
    ○ skipped got executed under UTC
    ○ skipped got executed under +5

```





















































































