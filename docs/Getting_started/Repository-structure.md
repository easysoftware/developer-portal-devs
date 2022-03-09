# Repository structure

*In this article we will show you how to get correct repository structures in the different environments.*

---

## Development Levels

Development of EasyRedmine is divided into four levels:

### Redmine

This is the main rail app on which our system is built. It's the forbidden zone, because if a new version of Redmine is released, we replace our Redmine with the new version and all changes are overwritten.

---

### Redmine plugins

Redmine has it's own plugin system. Plugins are located in `/plugins`.

You can create a plugin using:

```ruby
rails generate redmine_plugin PLUGIN_NAME
```

The plugin will consist of:

```ruby
|-- app            # Rails models, views, controllers, ...
|-- lib            # Libraries
|-- config         # Plugin configs
|   |-- locales    # Localizations
|   `-- routes.rb  # Routes
|-- db             # Migrations
|-- assets         # Javascripts, Stylesheets, Images
`-- init.rb        # Registration file, automatically loaded by Redmine
```

Most of these plugins have their own separate repositories.

Only own controllers and views handle code execution. Changes to other plugins are permitted only after previous consultation.

> We're moving towards 'product' specific plugins (eg for ER/EP). However, we're still maintaining a few existing, mostly because of marketing potential.

---

### Easy plugins

Changes for a specific client according to his needs. Coding takes place only in a generated folder `/plugins/easyproject/easy_plugins/modification_*`. Usage of your own controls and views is preferred. Modification of existing views is done by calling hooks.

The modification plugin can be generated using:

```ruby
rails generate redmine_extensions:plugin CLIENT_NAME --customer
```

<!-- theme: warning -->
> Changes in other plugins will be overwritten during update, so it's critical to only make changes in the folder mentioned above.
>
>Changes in core folders are permitted in only exceptional cases after previous approval.  **Author takes sole responsibility in those cases.**
>
>Functionality is checked mostly by the client. Because of minimal changes to core files there is little or no code review.

---

### Client modifications

Changes for a specific client according to his needs. Coding takes place only in a generated folder `/plugins/easyproject/easy_plugins/modification_*`. Usage of own controllers and views is preferred. Modification of existing views is done by calling hooks.
Modification plugin can by generated using:

```ruby
rails generate redmine_extensions:plugin CLIENT_NAME --customer
```
<!-- theme: warning -->
>Changes in other plugins will be overwritten during update, so it's critical to only make changes in the folder mentioned above.
>
>Changes in core folders are permitted in only exceptional cases after previous approval. **Author takes sole responsibility in those cases.**
>
>Functionality is checked mostly by the client. Because of minimal changes to core files there is little or no code review.

---

## Package repository

These package repositories contain built, ready-to-deploy applications (including compiled assets). It's actually rsync-ed from multiple development repositories into one and distributed to our clients from there.

### Easy Project:

https://git.easy.cz/easyproject/v11/stable ~ built from branch `master` **once a week**

https://git.easy.cz/easyproject/v11/bugs ~ built from branch `next/bugs` **once a day**

https://git.easy.cz/easyproject/v11/minor ~ built from branch `next/minor` **once a day**

https://git.easy.cz/easyproject/v11/major ~ built from branch `next/major` **once a day**

https://git.easy.cz/easyproject_com/stable ~ contains our current LTS version, only bugfixes outside of normal release cycle go there

### Easy Redmine:

https://git.easy.cz/easyredmine/v11/stable ~ built from branch `master` **once a week**

https://git.easy.cz/easyredmine/v11/bugs ~ built from branch `next/bugs` **once a day**

https://git.easy.cz/easyredmine/v11/minor ~ built from branch `next/minor` **once a day**

https://git.easy.cz/easyredmine/v11/major ~ built from branch `next/major` **once a day**

https://git.easy.cz/easyredmine/stable-2016 ~ contains our current LTS version, only bugfixes outside of normal release cycle go there

---

## Repos for client specific modifications

There are repositories for adjustments we made for some clients. It's usually a fork of some release state of platform, but with added customizations. 

---

## RYS repos

In RYS repositories there are always 2 protected branches - `master` and `devel`.

Our idea is that `master` branch contains stable version of the RYS, which is in production (clients application has ~> to this version).

<!-- theme: warning -->
> `Devel` is a branch for future features <- do merge-request against `devel`.

### Versioning

All our RYSes are versioned using semantic versioning.

First level - **MAJOR** - changes are release specific:
1. Whole new features
2. Refactoring
3. Breaking changes

Second level - **MINOR** - includes new small features:
1. Migrations
2. Minor changes of internal logic

Thirt level - **PATCH** (or BUG-FIX) - only for bug-fixing or harmless changes:

1. Documentation
2. Readme

<!-- theme: danger -->
>**Patch versions cannot include migrations and breaking changes!**