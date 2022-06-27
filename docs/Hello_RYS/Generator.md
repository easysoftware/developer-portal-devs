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
```

---

### Entity Generator

To generate new entity into RYS you can use our [Entity Generator](https://git.easy.cz/rysy/features/easy_entity_generator)

By default it generate whole CRUD, includes views with builder form, guessed fields and EasyQuery.
It generate also custom field class and basic tests.

You can install it by running
```ruby
bundle add easy_entity_generator --source https://gems.easysoftware.com
```

or 
```ruby
gem 'easy_entity_generator', git: 'git@git.easy.cz:rysy/features/easy_entity_generator.git', branch: 'master'
```

When you are done you can finally use the generator, by
- generating new entity

```ruby
rails g easy_entity_generator:entity [EntityName] [attribute_name:type]
```
<!-- theme: info -->
> Possible options are:
> --project 
> --author 
> --acts_as_customizable 
> --acts_as_searchable 
> --acts_as_activity_provider 
> --acts_as_attachable 
> --acts_as_event 
> --acts_as_tree 
> --acts_as_watchable 
> note: --author is default. If you want to skip it, use --no-author.

Example:
```ruby
rails g easy_entity_generator:entity Post name:string content:text
```

- Reverting generated entity

```ruby
rails destroy easy_entity_generator:entity [EntityName]
```

Example:

```ruby
rails destroy easy_entity_generator:entity Post
```
