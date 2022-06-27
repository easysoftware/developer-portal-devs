# User testing

*In this article we will show you how user testing works.*

---


User testing is done by our QA team. It is either deployment or release based. For bigger projects, dedicated testers can be assigned.

When you want your work on a task to be user tested, create a deployment a **change the task status to `'testing'` and assign it to an @QA team.**

Deployments are standalone isolated applications in our dev environment in our cloud. They are basically client applications with a copy of the database from "trials" and your source code.

On every task, in the `'more'` menu, there is a button `"New Deployment"`. It opens a modal window with a form:

1. '`URL`' is automatically pre-filled with the ID of the current task
2. '`Repository`' is, by default, `devel`. You can change it
3. '`Branch` is not limited to the ones in the selected, just copy your **feature branch** to the text field (without `origin/` part)
4. '`Host name`' is used as DB source,  by default, it's `mother.easyredmine.com` and you can use this in most cases
