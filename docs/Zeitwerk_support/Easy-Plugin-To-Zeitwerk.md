# How to make EasyPlugin compatible with Zeitwerk

1. Start with some automatic feedback
   1. We suggest to create a bash function
      ```bash
      zeit () {
        (
            rm -rf plugins/
            git checkout plugins/easyproject/
            rm -rf plugins/easyproject/easy_plugins
            git checkout plugins/easyproject/easy_plugins/easy_extensions
            git checkout plugins/easyproject/easy_plugins/easy_attendances
            git checkout plugins/easyproject/easy_plugins/easy_money
            git checkout plugins/easysoftware/Gemfile
            rm -f easy_engines/easy_extensions/app/models/easy_entity_imports/easy_ms_project_importer.rb
            find ./ -type f -name "*.zeitwerk" -exec sh -c 'mv "$1" "${1%.zeitwerk}"' _ {} \;
            echo "phoenix_gemfile = File.join(__dir__, \"Gemfile.zeit\")" >> Gemfile
            echo "eval_gemfile(phoenix_gemfile) if File.exist?(phoenix_gemfile)" >> Gemfile
        )
      }
      ``` 
      Add your plugin to the list of plugins to be checked out.
      e.g.: `git checkout plugins/easyproject/easy_plugins/MY_PLUGIN_NAME`
   2. create a new branch in https://git.easy.cz/devel/gitlab-ci-templates
   3. Add your plugin to the list of plugin in `easyredmine/zeitwerk.yml ` ATM around line 21
   4. in you branch in the platform modify .gitlab-ci.yml file like this:
      ```yaml
      include:
        project: devel/gitlab-ci-templates
        ref: MY_ZEITWERK_BRANCH_IN_GITLAB_CI_TEMPLATES
        file:
          - /platform-repository.yml
      variables:
        PHOENIX: "true"
      ```
   5. You should see zeitwerk jobs running in your CI pipeline
2. Fix all the errors
   1. Run `zeit` function
   2. Run `bundle update`
   3. Run `bundle exec rake rails c`
   4. Run `bundle exec rake rails c -e production`
   5. Run some test from your plugin
   6. Push and wait for CI to give you feedback
   
### Common Errors
- code in after_init.rb touches constants too early (see notes section below)
  - ATM try to wrap the code to `Rails.application.config.after_initialize` block
- undefined constants, usually EasyPageModules and EasyQueries
  - Move folder EasyPageModules and EasyQueries to app folder
- other undefined constants
  - All files in autoload_paths must be named after the module/class they contain
    - Fix this accordingly - we have quite a lot of typos in our codebase
  - Constant is defined inside of lib folder
    - either require this file where you need it or move it to a folder inside of app folder, e.g. app/utils
    
### Notes
- initialization process with zeitwerk will probably change in the future
  - the plan is to split after_init.rb to multiple files similar to initializers in easy_engines and require them during the adding easy_plugins to autoload_paths
  - this means that the same rules will apply to these init files as to initializers inside of easy_engines
