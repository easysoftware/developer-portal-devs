# Frontend testing

*In this article we will show you how to test your FE services.*

---



## Frontend tests

We are using [Jest](https://jestjs.io/), for both platform and plugins (mainly Vue.js)

You can run them from the source root as:

```
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
