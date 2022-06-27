# Backend best practices

*In this article we will show you some of our best practices.*

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
