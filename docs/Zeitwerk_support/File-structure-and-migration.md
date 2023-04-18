# File structure and Migration

*This guide describes our file structure (old and new), and also some steps for making it work*

## Zeitwerk

We are gradually switching from the old way of doing things to being able to enable
[Zeitwerk](https://guides.rubyonrails.org/classic_to_zeitwerk_howto.html. That requires a dramatic change in file
structure.
It will be a long process and in the meantime we need to ensure backwards compatibility.
Below is a guideline on what the new structure should look like and some steps to help you with making this transition.

### Directory structure

The main thing we lack to support Zeitwerk conventions is **proper file naming** (module scoping and one module/class
per file, matching it's name) and thus enabling Zeitwerk _autoloading_. To make it work we decided to move all our
plugins from the `plugins/` directory into a new `easy_engines/` directory and turn them into rails _Engines_.
Other notable changes include proper separation of `initializers`, old `migrations` timestamping, unified `spec_helper`
requiring.

###### Platform

Redmine already supports Zeitwerk from version `5.0.0` so there are only a few changes outside of our plugins/engines.

###### Plugins (legacy)

The idea is that when this transition is over all legacy **plugins** will have been moved from the `plugins/` directory
into
the `easy_engines/` directory.

###### Engines

**Engines** are our legacy plugins in a new coat, after they go through the migration steps below. They reside in
the `easy_engines/` directory in app ROOT.

###### RYSes

**RYSes** keep their current structure, but some migration steps below might be necessary to keep zeitwerk
compatibility.

### Load order

With these changes we need to again reevaluate and ensure correct load order, mainly concerning patches.\
A couple of points to follow:

- `RedmineExtensions::Reloader.to_prepare` has been deprecated, replace it based on order you need,
  usually `Rails.application.config.after_initialize` works fine

### Plugin-to-Easy-Engine migration steps

Point-by-point guide to turning a _plugin_ into a zeitwerk compatible _engine_.

- App directory
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
- Initializers
    - `easy_extensions` initializers (`easy_engines/easy_extensions/config/initializers/`) have been refactored into
      many smaller files that serve as a template for **where to put each initialization step**
    - Default `EasySetting` values are set in `easy_engines/easy_extensions/config/initializers/08_easy_settings.rb`,
      DON'T use migrations. If you find `EasySetting` being created inside a migration move it to an initializer named
      `08_easy_settings.rb`(don't be afraid to move other ones down if they already use `08` and higher)
- Dependencies
- Migrations
- Tests

### Extra info for context

This section contains more details about the changes done during core zeitwerk migration to provide more context as to
why certain changes were made.

Notable changes:

- Initializers
    - Two new initializers have been added to the root `config/initializers/` and are used to control the load order
- Dependencies
    - Gem [redmine_extensions](https://github.com/easyredmine/redmine_extensions) has been gutted with most of
      the functionality moved into `easy_extensions` engine
- Migrations
    - Old migration files starting with a three digit number sequence instead of a timestamp were incompatible
      and are remapped and updated when running migration tasks. Script used -`bin/upgrade_easy_plugin_migrations.rb`
    - A new module for controlling easy engine migrations has been defined in `easy_engines/lib/easy_data_migrations.rb`
- Tests
    - `rails_helper` has been moved to `/easy_engines/easy_extensions/spec/`
