# Easy Project initialization process

*In this article we try to explain the order in which is EasyProject initialized*

1. Engine Initializers #1
   * code in `lib/EngineName/engine.rb`, e.g. `lib/easy_b2b/engine.rb`
2. Engines Initializers #2
    * initializer code blocks from `lib/EngineName/engine.rb`
      ```ruby
      class Engine < ::Rails::Engine
         initializer 'My initializer' do
           # Custom initializer
         end
      end
      ```
3. Rails application initializers
   * `config/initializers/*`
4. Engines Initializers #3
   * `config/initializers/*`
5. `ActiveSupport::Reloader.to_prepare` blocks
   * These are run on every code reload in development mode
6. `Railtie.config.to_prepare` blocks
   * These are run on every code reload in development mode
   * Redmine plugins are loaded in this block
   * This blocks are pushed to the end of `ActiveSupport::Reloader.to_prepare` blocks right before `ActiveSupport::Reloader.to_prepare` blocks are executed
   1. `ActiveSupport.run_load_hooks :after_plugins_loaded`
      * Easy hack to call our code right after redmine loaded plugins
      1. `ActiveSupport.run_load_hooks(:easyproject)`
      2. `ActiveSupport.run_load_hooks(:after_apply_easy_patches, self)`
      3. `ActiveSupport.run_load_hooks(:easy_project_loaded)`
7. `Railtie.config.after_initialize` blocks
