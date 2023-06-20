# Frontend first steps

*In this article we will guide you through the first steps to develop frontend features.*

---

## First steps as Frontend developer

<!-- theme: danger -->
> Note that for in order to be able to develop frontend you also have to be able to have backend running on your workstation. Head [here](https://easysoftware.stoplight.io/docs/developer-portal-devs/docs/Getting_started/Initial-setup.md) to set it up in case you haven't

We are gradually switching from rending everything on the server to using a modern JS framework in combination with GraphQL on the back-end. For that we picked the [Vue.js](https://vuejs.org/) framework.

All you need is contained in platform, except of legacy plugins like Gantt, WBS, or Scheduler.

### How to install EASY VUE?

1. Install node.js.

> Please [install `nvm`](https://github.com/nvm-sh/nvm), version of node is defined in `.nvmrc`

```bash
nvm use
```

Command above will switch the node version to the version that is defined in `.nvmrc`. You may need to install the version of the node according to nvm, please see [README.md](https://github.com/nvm-sh/nvm/blob/master/README.md)

2. Install as public package (if you don't have it already)

```
npm install --global yarn
```

3. To be able to install private Easy packages, follow [these instructions](https://nodes.easysoftware.com/-/help). (instructions are now for npm but commands are same for yarn)

### Building Vue

Running in the root of platform

```
bundle exec rake easyproject:install RAILS_ENV=development
```

This command will install `yarn modules` and `compile Vue`. This is used in the pipelines.

### Rails ujs

Rails-ujs is a javascript library that allows non-GET requests from HTML links, Ajax integration with Rails, and more.
To make Rails-ujs work on localhost with Vite we need to replace the content `/app/frontend/src/rails_ujs.js` with:

```javascript
// without vite dev server running

// import "@rails/ujs";

// with vite dev server running

import Rails from "@rails/ujs"

window.Rails = Rails;
window.Rails.start();
```

### Development on your local machine

> Make sure that your platform is in the proper branch.

1. Install dependencies in the platform.

```
yarn
```

2. Start Vite to serve sources

```
vite dev
```

<!-- theme: danger -->
> Do not kill it! Keep an eye on the console. Front compilation failures appear here.

3. Launch rails server in another console

```
bundle exec rails server
```

> From this point, you don't have to do anything with the backend. As long as it's running, it's fine. The server listens by default at http://localhost:3000/.

4. Use the app in the browser

> Vite is now serving the frontend assets. Its watching changes in the filesystem and recompiles new versions on the fly.

#### Unit tests

Unit tests can be find [here](https://vue-test-utils.vuejs.org/) and [here](https://jestjs.io/).

#### Code formatting

We use ESLint which does removes all original styling and ensures that all outputted code conforms to a consistent style.
ESLint takes your code and reprints it from scratch by taking the line length into account.

Find out how to use it [here](https://eslint.org/docs/user-guide/getting-started).
