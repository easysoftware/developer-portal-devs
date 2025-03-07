# Frontend testing

*In this article we will show you how to test your FE services.*

---

## Tests Structure

### Base Test Structure
```
app/frontend/src/tests/
   ├── <module_name>/  # Tests for a specific module, contains subset of tests module structure
   ├── shared/         # Shared between tests modules, contain subset of tests module structure
```

### Module Test Structure
```
<module_name>/
├── fixtures/  # Test fixtures
├── mocks/     # Test mocks
├── stubs/     # Test stubs
├── tests/     # Unit and integration tests, can be splitted into separate folders from module structure, but does not have to
├── types/     # TypeScript interfaces & types & enums, typically enum with tests selectors
├── utils/     # Utility functions for tests
```

## Frontend tests

We are using [Vitest](https://vitest.dev/), for both platform and plugins (mainly Vue.js)

You can run them from the source root as:

```
yarn test

# or tests with coverage
yarn test:coverage    

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
