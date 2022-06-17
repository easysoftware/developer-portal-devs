# RYS Generator

*In this article you will learn how RYS Generator works.*

---

### What is RYS Generator?

The RYS generator is our product and is primarily for creating a new RYS into the EasySoftware platform. When creating a new RYS using a generator, a new RUBY barebone structure is created in a separate GIT repository.

---

### Plugin

```ruby
rails generate rys:plugin --help
rails generate rys:plugin NAME
```

The target directory is evaluated in this order: 
1.  `--path` options
2.  `RYS_PLUGINS_PATH` environment variable
3.  `RAILS_ROOT/rys_plugins`

Gem is also added into `Gemfile.local`

---

### Patches
```ruby
rails generate rys:patch TYPE PLUGIN NAME
rails generate rys:patch m RYS_PLUGIN_NAME Issue
rails generate rys:patch model RYS_PLUGIN_NAME Issue
rails generate rys:patch helper RYS_PLUGIN_NAME Issues
```

Or you can use interactive generator:
```ruby
rails generate rys:ipatch
```
---

### Rails generators

You can use the same generator like in any Rails plugin. Just add prefix `rys` and specify Rys plugin name.

```ruby
rails generate rys:model RYS_PLUGIN_NAME ...normal arguments...
rails generate rys:scaffold RYS_PLUGIN_NAME ...normal arguments...
rails generate rys:controller RYS_PLUGIN_NAME ...normal arguments...
