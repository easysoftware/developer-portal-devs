# Repository Preparation for Custom Branding

## Transition from V13 to V14

1) In Esko, locate the client’s contract ID. From now on referred to as `CONTRACT_ID`

2) Create client repository  
   Use
   the ['Create Repository' action pipeline](https://git.easy.cz/devops/repo-bot-actions/create-repository/-/pipelines/new).
   Fill in the `CONTRACT_ID` and run pipeline.
   This action will create a clients repository. The link to it can be found at the end
   of the pipeline:  
   `[RepoBot::Services::Cli::CreateRepository] New project is ready on https://git.easy.cz/clients/my_little_pony`.

3) Create update template  
   Now, we need to create an update template
   in [Repo-bot](https://git.easy.cz/devops/repo-bot/-/tree/master/templates/update).
   The naming convention requires that templates be named `CONTRACT_ID.yml`.

   This ensures that during updates, the modification plugin and most importantly the style adjustments, which are made
   in:  
   `app/frontend/src/shared/stylesheets/easy/themes/_theme--brand.scss`

   Since the above-mentioned stylesheet (`_theme--brand.scss`) does not exist in V13, it is necessary to include this
   file in the update during the first run. Therefore, the update template for the first run will look like this:

   ```yaml
   source:
    gitlab_repository: git@git.easy.cz:easyproject/v14/stable.git OR git@git.easy.cz:easyredmine/v14/stable.git based on the client
    gitlab_branch: master
    do_not_copy:
    - .gitlab
    - .github
    - _package
    # - app/frontend/src/shared/stylesheets/easy/themes/_theme--brand.scss
   
   target:
    project_id: GITLAB_PROJECT_ID (can be found in gitlab projects settings/general)
    merge_request_auto: 1
    do_not_delete:
    - .git
    # - app/frontend/src/shared/stylesheets/easy/themes/_theme--brand.scss
    - plugins/easyproject/easy_plugins/modification_*
   ```

   We commit and push this template to the Repo-bot master. We wait for
   the [Repo-bot pipeline](https://git.easy.cz/devops/repo-bot/-/pipelines) to release the Docker image.

4) Run client repository update  
   Use
   the ['Update Repository' action pipeline](https://git.easy.cz/devops/repo-bot-actions/update-repository/-/pipelines/new).
   Fill in the `CONTRACT_ID` and run pipeline. This action will create a merge request in the client’s repository with
   the update into the master branch.

5) In the [Repo-bot](https://git.easy.cz/devops/repo-bot)
   we uncomment the two commented lines in our update template. Then we commit and push the final version of the
   template again. The final version should look something like this:

   ```yaml
   source:
     gitlab_repository: git@git.easy.cz:easyproject/v14/stable.git OR git@git.easy.cz:easyredmine/v14/stable.git based on the client
     gitlab_branch: master
     do_not_copy:
       - .gitlab
       - .github
       - _package
       - app/frontend/src/shared/stylesheets/easy/themes/_theme--brand.scss
   target:
     project_id: GITLAB_PROJECT_ID (can be found in gitlab projects settings/general)
     merge_request_auto: 1
     do_not_delete:
       - .git
       - app/frontend/src/shared/stylesheets/easy/themes/_theme--brand.scss
       - plugins/easyproject/easy_plugins/modification_*
   ```

6) Add modification plugin  
   Use
   the ['Add Modification Plugin' action pipeline](https://git.easy.cz/devops/repo-bot-actions/add-modification-plugin/-/pipelines/new).
   Fill in the `CONTRACT_ID` and run pipeline. This action will create a merge request with the modification plugin in
   the client’s repository into the master branch.

## Custom Branding for V14

In this case, we don't need to run the update with commented out lines. So in step 3, create the final update template
(step 5) right away.
