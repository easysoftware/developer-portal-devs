# Repository Preparation for Customizing Theme Design

## Transition from V13 to V14

1) In Esko, locate the client’s contract ID. From now on referred to as `CONTRACT_ID`  
   [Contract Link](https://es.easyproject.com/easy_contracts/ID)

2) Fill in the `CONTRACT_ID` value at the following link and submit the form.  
   [GitLab Link](https://git.easy.cz/devops/repo-bot-actions/create-repository/-/pipelines/new)  
   This step will trigger a GitLab pipeline that creates the client's repository. The link to it can be found at the end
   of the pipeline:  
   `[RepoBot::Services::Cli::CreateRepository] New project is ready on https://git.easy.cz/clients/my_little_pony`

3) Creating an update template  
   Now, we need to create an update template in the Repo-bot  
   [RepoBot on GitLab](https://git.easy.cz/devops/repo-bot/-/tree/master/templates/update)  
   The naming convention requires that templates be named `CONTRACT_ID.yml`.

   This ensures that during updates, the modification plugin and most importantly the style adjustments, which are made
   in:  
   `app/frontend/src/shared/stylesheets/easy/themes/_theme--brand.scss`

   Since the above-mentioned stylesheet (`_theme--brand.scss`) does not exist in V13, it is necessary to include this
   file in the update during the first run. Therefore, the update template for the first run will look like this:

   ```yaml
   source:
    gitlab_repository: EASYREDMINE/EASYPROJECT /v14/stable.git
    gitlab_branch: master
    do_not_copy:
    - .gitlab
    - .github
    - _package
    # - app/frontend/src/shared/stylesheets/easy/themes/_theme--brand.scss
   
   target:
    project_id: GITLAB_PROJECT_ID(gitlab/client_repository/settings/general/Project ID
    merge_request_auto: 1
    do_not_delete:
    - .git
    # - app/frontend/src/shared/stylesheets/easy/themes/_theme--brand.scss
    - plugins/easyproject/easy_plugins/modification_*
   ```

   We commit and push this template to the Repo-bot master. We wait for the Repo-bot pipeline  
   [RepoBot pipeline](https://git.easy.cz/devops/repo-bot/-/pipelines)  
   to release the Docker image.

4) Running the client repository update

   Using the `Update Repository` action  
   [RepoBot Update](https://git.easy.cz/devops/repo-bot-actions/update-repository/-/pipelines/new)  
   we trigger the update of the client's repository. Before submitting, we again fill in the `CONTRACT_ID` value just
   like during the repository creation.
   This action will create a merge request in the client’s repository with the update into the master branch.

5) In the Repo-bot  
   [RepoBot on GitLab](https://git.easy.cz/devops/repo-bot)  
   we uncomment the two commented lines in our update template. Then we commit and push the final version of the
   template again.
   The final version should look something like this:

   ```yaml
   source:
     gitlab_repository: EASYREDMINE/RASYPROJECT /v14/stable.git
     gitlab_branch: master
     do_not_copy:
       - .gitlab
       - .github
       - _package
       - app/frontend/src/shared/stylesheets/easy/themes/_theme--brand.scss
   target:
     project_id: GITLAB_PROJECT_ID(gitlab/client_repository/settings/general/Project ID
     merge_request_auto: 1
     do_not_delete:
       - .git
       - app/frontend/src/shared/stylesheets/easy/themes/_theme--brand.scss
       - plugins/easyproject/easy_plugins/modification_*
   ```

6) Creating a modification plugin

   We clone the client's repository and create a modification plugin in the updated code at the path
   `plugins/easyproject/easy_plugins`.
   The structure and content of the file:
   ```
   modification_CLIENT_NAME/
   ├── config/
   │   └── initializers/
   │       └── logos_config.rb
   ├── images/
   │   └── .keep
   ├── lib/
   │   └── modification_CLIENT_NAME.rb
   └── init.rb
   ```

   #### logos_config.rb
   ```ruby
   # Rails.application.config.after_initialize do
   #   require "easy_extensions/logo"
   #   EasyExtensions::Logo.configure do |config|
   #     config.logos_folder_path = File.expand_path(File.join(__dir__, "../../images")) # path into logos folder
   #     config.path = Rails.root.join(config.logos_folder_path, "client_logo.jpeg") # email logo
   #     config.variables = {
   #       logo_url: "client_logo.jpeg",
   #       logo_inverted_url: "client_logo.jpeg",
   #       logo_mark_url: "client_logo.jpeg",
   #       logo_inverted_mark_url: "client_logo.jpeg",
   #       logo_mutation_url: "client_logo.jpeg",
   #       login_bg_url: "client_logo.jpeg",
   #     }
   #   end
   # end
   ```
   #### modification_CLIENT_NAME.rb
   ```ruby
   module ModificationClientName
   end
   ```
   #### init.rb
   ```ruby
   Redmine::Plugin.register :modification_CLIENT_NAME do
    name "CLIENT NAME"
    author "Easy Software Ltd"
    description ""
    url "www.easyredmine.com"
    author_url "www.easysoftware.cz"
    should_be_disabled false
    
    plugin_in_relative_subdirectory File.join("easyproject", "easy_plugins")
   end
   ```

## Customization Theme Design for V14

In this case, it's sufficient to follow steps 1 and 2 from the previous process. Step 3 should be done without the
commented lines containing `_theme--brand.scss`. And also step 6.
