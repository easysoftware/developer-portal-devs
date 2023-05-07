# File structure and Transition

*This guide is all about our transition to enable Zeitwerk in EP/ER. It goes over some details about Zeitwerk and how it
impacts our application. Then outlines steps for making this transition, and ends with some extra info for those who
want to know more context behind individual changes.*

## Zeitwerk

We are gradually switching from the old way of doing things to being able to enable
[Zeitwerk](https://guides.rubyonrails.org/classic_to_zeitwerk_howto.html). That requires a dramatic change in file
structure. It will be a long process and in the meantime we need to ensure backwards compatibility. Below is a guideline
on what the new structure should look like and some steps to help you with making this transition. Version `v13` of
EP/ER will support both old and new structure with Zeitwerk **disabled** and deprecation warnings in place. In `v14` we
plan a hard switch to **enabled** Zeitwerk and no longer supporting the old structure.

### Directory structure

The main thing we lack to support Zeitwerk conventions is **proper file naming** (module scoping and one module/class
per file, matching it's name) and thus enabling Zeitwerk _autoloading_. To make it work we decided to move all our
plugins from the `plugins/` directory into a new `easy_engines/` directory and turn them into rails _Engines_.
Other notable changes include proper separation of `initializers`, refactoring of `lib/` and `patches/` directories,
old `migrations` timestamping, unified `spec_helper`
requiring.

###### Redmine

Redmine already supports Zeitwerk from version `5.0.0` so there are only a few changes outside of our plugins/engines.

###### Plugins (legacy)

The idea is that when this transition is over all legacy **plugins** will have been moved from the `plugins/` directory
into
the `easy_engines/` directory.

###### Engines

**Engines** are our legacy plugins in a new coat after they go through the transition steps below. They reside in
the `easy_engines/` directory in app ROOT.

###### RYSes

**RYSes** keep their current structure, but some transition steps below might be necessary to keep Zeitwerk
compatibility.

### Load order

With these changes we need to again reevaluate and ensure correct load order, mainly concerning patches.\
A couple of points to follow:

- `RedmineExtensions::Reloader.to_prepare` has been deprecated. Replace it based on order you need,
  usually `Rails.application.config.after_initialize` or `ActiveSupport::Reloader.to_prepare` works fine

### Deprecation warnings

We've added custom deprecation warnings to make it easier to recognize many, but not all, of the pieces of code that
need to be addressed.
> Custom deprecation warnings can be silenced by setting the `SILENCE_EASY_DEPRECATIONS` environment variable to `true`.

### Plugin-to-Easy-Engine transition steps

Point-by-point (in no particular order) guide to turning a _plugin_ into a Zeitwerk compatible _engine_.
<!-- theme: danger -->
> To keep **git history** for files moved between directories, use `git mv` or _drag&drop_ instead of copying and
> pasting
> which in most IDEs will create a new file **losing all previous history**.

- **App directory**
    - All modules/classes must be properly named
        - Each file needs to contain only a **single module/class matching the file's name**
        - **Module scoping**\
          File inside `easy_engines/my_cool_plugin/models/pages/first_page.rb` needs to look like:
          ```ruby
          module Pages
            class FirstPage
          
            end
          end
          ```
        - **Exceptions** can be defined in `easy_engines/my_cool_plugin/lib/my_cool_plugin/engine.rb`\
          This way you can get around module scoping, which we used for these most used models to avoid rewriting
          everything in `easy_extensions`
          ```ruby
          paths['app/models'] << 'app/models/api_services_for_exchange_rates'
          paths['app/models'] << 'app/models/easy_entity_actions'
          paths['app/models'] << 'app/models/easy_page_modules'
          paths['app/models'] << 'app/models/easy_queries'
          paths['app/models'] << 'app/models/easy_rakes'
          ```
- **Initializers**
    - `easy_extensions` initializers (`easy_engines/easy_extensions/config/initializers/`) have been refactored into
      many smaller files that serve as a template for **where to put each initialization step**
    - Default `EasySetting` values are set in `easy_engines/easy_extensions/config/initializers/08_easy_settings.rb`,
      DON'T use migrations. If you find `EasySetting` being created inside a migration move it to an initializer named
      `08_easy_settings.rb`(don't be afraid to move other ones down if they already use `08` and higher)
    - _Stop touching constants_. Remove all constant touching in the initialization phase
- **Lib**
  > Many of our plugins are using `lib` as a dumping ground for all sorts of modules, classes and scripts relying
  on `lib` being autoloaded. That will no longer be the case. `Lib` should only contain files that are loaded manually
  when needed. The rest should be distributed to other autoloaded directories.
    - To start, rename the `lib/` directory to `easy_lib/` which will be autoloaded to keep backwards compatibility
    - Then create a new `lib/` and start moving files from `easy_lib/` to directories where when belong
      until `easy_lib/` is empty and you can delete it
    - Don't forget to add explicit requires where needed since `lib` is not autoloaded anymore
- **Patches**
    - Similar to _Lib_, start by renaming `patches/` to `easy_patches/` and creating a new `patches/` directory
    - Then rewrite each patch to use `Rys::Patcher` according to https://github.com/easysoftware/rys/wiki/Patches and
      move it into patches until `easy_patches/` is empty and you can delete it
- **Dependencies**
- **Migrations**
    - Make sure your migrations are divided to **schema** and **data** migrations and inherit from the respective
      classes to
      ensure migration order:
        - schema migrations in `db/migrate/` directory inheriting from `ActiveRecord::Migration[6.1]`
        - data migrations in `db/data/` directory inheriting from `EasyExtensions::EasyDataMigration`
        - **RYSes** have an extra directory `db/after_plugins/` where you should put all **schema** migrations that need
          to run after all platform engines' migrations
- **Tests**

### Extra info for context

This section contains more details about the changes done during core Zeitwerk transition to provide more context as to
why certain changes were made.

Notable changes:

- Deprecation warnings
    - Specific custom deprecation warnings can be found in `easy_engines/lib/redmine_extensions/reloader.rb`
      and `easy_engines/lib/legacy_patch_manager_bypass.rb`
- Initializers
    - Two new initializers have been added to the root `config/initializers/` and are used to control the load order
- Dependencies
    - Gem [redmine_extensions](https://github.com/easyredmine/redmine_extensions) has been gutted with most of
      the functionality moved into the `easy_extensions` engine
- Migrations
    - Old migration files starting with a three digit number sequence instead of a timestamp were incompatible
      and are remapped and updated when running migration tasks. Script used -`bin/upgrade_easy_plugin_migrations.rb`
    - A new module for controlling easy engine migrations has been defined in `easy_engines/lib/easy_data_migrations.rb`
- Tests
    - `rails_helper` has been moved to `/easy_engines/easy_extensions/spec/`
