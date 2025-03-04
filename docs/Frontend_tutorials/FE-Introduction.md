# Frontend Introduction

*This guide walks you through the initial steps required to set up and develop frontend features in our project.*

---

## Getting Started as a Frontend Developer

We are transitioning from server-side rendering to a modern frontend stack using [Vue.js](https://vuejs.org/) alongside GraphQL on the backend.

<!-- theme: danger -->
> Note: To develop frontend features, you must also have the backend running on your workstation. Follow the [Initial Setup Guide](https://easysoftware.stoplight.io/docs/developer-portal-devs/docs/Getting_started/Initial-setup.md) if you haven't set it up yet.

---

## Setting Up the Frontend Environment

### 1. Install Node.js.

We use [**Node Version Manager (nvm)**](https://github.com/nvm-sh/nvm) to manage Node.js versions. [Install `nvm`](https://github.com/nvm-sh/nvm?tab=readme-ov-file#installing-and-updating) if you haven't already. Once installed, switch to the correct Node.js version:

```bash
nvm use
```

> If the required version is not installed, `nvm` will prompt you to install it. Refer to the [`nvm` README](https://github.com/nvm-sh/nvm/blob/master/README.md) for installation details.

### 2. Use the right version of Yarn package manager

We use [**Yarn**](https://yarnpkg.com/) as the package manager. Enable it with:

```bash
corepack enable
```

> [Corepack](https://nodejs.org/api/corepack.html#corepack) will switch to Yarn version that is defined in `package.json` in field `packageManager`.

### 3. Running Initial Setup

To initialize the project, including database migration and installing dependencies (node modules), run the following command in the project root:

```bash
bundle exec rake easyproject:install RAILS_ENV=development
```

## Running the Development Environment

> Before starting development, ensure that your **platform is on the correct branch**.

### 1. Start Vite Development Server

Start Vite to serve the frontend assets, with watching changes:

```bash
vite dev
```
> It is possible to run `vite build` to build the frontend assets without watching changes. (This is used in production.)

<!-- theme: danger -->
> Do not kill it! Keep an eye on the console. Front compilation failures appear here.

### 2. Start the Rails server

In a separate terminal, start the Rails backend:

```bash
bundle exec rails server
```

### 3. Use the app in the browser

Once the servers are running:
- **Vite** serves the frontend assets.
- Any changes to the source files trigger automatic recompilation.
- The app can be accessed via **http://localhost:3000/**.
