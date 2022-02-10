# Frontend testing

*In this article we will show you how to test your FE services...*

---



## Frontend tests

Nowadays we are using [Jest](https://jestjs.io/), for both platform and plugins (mainly Vue.js)

You can run them from the source root as:

```ruby
npm test
# just particular
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

## User tests

User testing is done by our QA team. It is either deployment or release based. For bigger projects, dedicated testers can be assigned.

**// K ODSTRANĚNÍ???**

When you want your work on a task to be user tested, create a deployment a **change the task status to `'testing'` and assign it to an @QA team.**

Deployments are standalone isolated applications in our dev environment in our cloud. They are basically client applications with a copy of the database from "trials" and your source code.

On every task, in the `'more'` menu, there is a button `"New Deployment"`. It opens a modal window with a form:

1. '`URL`' is automatically pre-filled with the ID of the current task,
2. '`Repository`' is, by default, "devel". You can change it.
3. '`Branch`' is not limited to the ones in the selected, just copy your **feature branch** to the text field (without `origin/` part)
4. '`Host name`' is used as DB source,  by default, it's `mother.easyredmine.com` and you can use this in most cases.
