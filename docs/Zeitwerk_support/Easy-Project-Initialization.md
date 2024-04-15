# Easy Project initialization process

*In this article we try to explain the order in which is EasyProject initialized with Zeitwerk enabled*

1. Engine Initializers #1
   * code in `lib/EngineName/engine.rb`, e.g. `lib/easy_b2b/engine.rb`
   * code that is not in any later blocks
2. Rails application initializers
   * `config/initializers/*`
   * code that is not in any of the later blocks
3. Engines Initializers #2
   * initializer code blocks from `lib/EngineName/engine.rb`
     ```ruby
     class Engine < ::Rails::Engine
        initializer 'My initializer' do
          # Custom initializer
        end
     end
     ```
4. Engines Initializers #3
   * `config/initializers/*`
5. `Rails.application.config.to_prepare` blocks
   * These are run on every code reload in development mode
   * This block runs before patches are applied !!
6. `Railtie.config.to_prepare` blocks
   * These are run on every code reload in development mode
   * Redmine plugins are loaded in this block
     * in Rails.application.config.to_prepare 
     * this means that EasyB2b::Engine.config.to_prepare will be called after plugin application if defined in easy_b2b/config/initializers
     * if defined in easy_b2b/lib/easy_b2b/engine.rb then it will run before patches
7. `Rails.application.config.after_initialize` blocks

### Example
1. ```ruby
   # Rys/lib/EngineName/engine.rb
   class Rys < ::Rails::Engine
      puts "This runs right away"
   end
   ```
2. ```ruby
   # Rails.root/config/initializers/30-redmine.rb
    puts "This is where plugins/PLUGIN_NAME/app/* + lib is added to autoload_paths"
   ```
3. ```ruby
   # Rys/lib/EngineName/engine.rb
   class Rys < ::Rails::Engine
     initializer do
       puts "Enqueued but runs very early"
     end
   end
   ```
4. ```ruby
   # Rys/config/initializers/03_hooks.rb
    puts "Enqueued but runs very early"
   ```
5. ```ruby
   # Rails.root/config/initializers/00_core_plugins.rb
    Rails.application.config.to_prepare do
      puts "This is before easy patch application"
    end
   ```
6. ```ruby
   # Rys/config/initializers/03_hooks.rb
    Rails.application.config.to_prepare do
      puts "This is before easy patch application"
    end
   ```
7. ```ruby
   # Rys/lib/EngineName/engine.rb
   class Rys < ::Rails::Engine
     config.to_prepare do
       puts "This is still before easy patch application"
     end
   end
   ```
8. ```ruby
    # Rails.root/config/initializers/30_redmine.rb
    # inside Redmine::PluginLoader.load
    Rails.application.config.to_prepare do
      puts "In this block redmine plugin initializers are run"
    end
    # Rails.root/config/initializers/33_patches.rb
    Rails.application.config.to_prepare do
      puts "In this block we apply easy_patches"
    end
   ```
9. ```ruby
    # Rys/config/initializers/03_hooks.rb
    Rys::Engine.config.to_prepare do
      puts "Runs as #1 out of these 3"
    end
    Rails.application.config.to_prepare do
      puts "Runs as #2 out of these 3"
    end
    Rys::Engine.config.to_prepare do
      puts "Runs as #3 out of these 3"
    end
   ```
10. ```ruby
    # Rys/lib/EngineName/engine.rb
    class Rys < ::Rails::Engine
      config.after_initialze do
        puts "This is the first after_initialize block"
      end
    end
    ```
11. ```ruby
    # Rails.root/config/30-redmine.rb
    Rails.application.config.to_prepare do
      puts "After initialize block from core app"
    end
    ```
12. ```ruby
    # Rys/config/initializers/03_hooks.rb
    Rails.application.config.to_prepare do
      puts "After initialize block from engine initializer files"
    end
    ```
