# Frontend first steps

The beginning of an awesome article...

---

## First steps as Frontend developer

We are gradually switching from rending everything on the server to using a modern JS framework in combination with GraphQL on the back-end. For that we picked the [Vue.js](https://vuejs.org/) framework.

Plugin that contains all you need to get it running is `easy_vue`.

### How to install EASY VUE? 

1. Install node.js. 
>   Please [install `nvm`](https://github.com/nvm-sh/nvm) as a first step and go by `README.md` to install the latest stable version 14 of Node.js. 
2. Install [npm](https://docs.npmjs.com/getting-started) (Node Package Manager)
3. Clone the [Easy Vue repository](https://git.easy.cz/devel/easy-vue.js).
4. To be able to install private Easy npm packages, folow [these instructions](https://nodes.easysoftware.com/-/help).

### Buillding Vue

Running in the root of platform 

```ruby
bundle exec rake easyproject:install RAILS_ENV=development
```

This command will install `npm modules` and `compile Vue`. This is used in the pipelines.

### Development on your local machine


#### Prepare sources locally

1. Make sure that your platform is in the proper branch.
2. Make sure that your `easy_vue.js` is in the proper branch (it follows the same branching pattern as a platform, eg. it's not RYS).
3. [Link](https://git.easy.cz/internal/easy_cli) the `easy_vue` plugin to the platform. 

> You can do it manually via symling the vue repo into [platform]/plugins/easy_vue too. 

#### Start the webpack for serving frontend

1. Install dependencies for `plugin/easy_vue` in the platform, or in the original `easy_vue.js` repo directory. 

```ruby
npm --prefix plugins/easy_vue ci
```

2. Start webpack to serve sources

```ruby
npm --prefix plugins/easy_vue run dev
```

<!-- theme: danger -->
> **Do not kill it!** Keep an eye on the console. Front compilation failures appear here.

3. Launch rails server in another console

```ruby
bundle exec rails server
```
> From this point, you don't have to do anything with the backend. As long as it's running, it's fine. The server listens by default at http://localhost:3000/.

4. Use the app in the browser 

> Webpack is now serving the assets for `easy_vue` repo. Its watching changes in the filesystem and recompiles new versions on the fly. 

#### Unit tests

Unit tests can be find [here](https://vue-test-utils.vuejs.org/) and [here](https://jestjs.io/). 

#### Code formatting

We use Prettier which does removes all original styling and ensures that all outputted code conforms to a consistent style.
Prettier takes your code and reprints it from scratch by taking the line length into account.

> Find out how to use it [here](https://prettier.io/docs/en/install.html). 
