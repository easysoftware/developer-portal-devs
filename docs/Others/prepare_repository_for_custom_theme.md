# Příprava repozitáře pro customizaci vzhledu

## Přechod z V13 na V14

1) V Esku si najdeme ID kontraktu klienta. Nadále označené jako `CONTRACT_ID`  
   [Odkaz na kontrakt](https://es.easyproject.com/easy_contracts/ID)

2) Na následujícím odkazu vyplníme hodnotu `CONTRACT_ID` a odešleme formulář.  
   [Odkaz na GitLab](https://git.easy.cz/devops/repo-bot-actions/create-repository/-/pipelines/new)  
   Tento krok spustí GitLab pipeline, která vytvoří klientův repozitář. Odkaz na něj najdeme na konci pipeline:  
   `[RepoBot::Services::Cli::CreateRepository] New project is ready on https://git.easy.cz/clients/my_little_pony`

3) Vytvoření update templaty
   Nyní musíme do Repo-bota  
   [RepoBot na GitLab](https://git.easy.cz/devops/repo-bot/-/tree/master/templates/update)  
   vytvořit update templatu. Názvová konvence templát vyžaduje, aby se jmenovaly `CONTRACT_ID.yml`.

   Tato zajistí, že se při updatech nesmaže modifikační plugin a hlavně úpravy stylů, které se dělají do:  
   `app/frontend/src/shared/stylesheets/easy/themes/_theme--brand.scss`

   Jelikož ve V13 neexistuje výše zmíněný stylovací soubor (`_theme--brand.scss`), je potřeba při prvním updatě tento
   soubor z update nevyloučit. Tedy update templata při prvním spuštění bude vypadat takto:

   ```yaml
   source:
    gitlab_repository: EASYREDMINE/RASYPROJECT /v14/stable.git
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

   Tuto templatu commitneme a pushneme do masteru Repo-bota. Počkáme, až pipeline Repo-bota  
   [RepoBot pipeline](https://git.easy.cz/devops/repo-bot/-/pipelines)  
   releasne Docker image.

4) Spuštění updatu klientova repozitáře

   Pomocí `repobotUpdate` akce  
   [RepoBot Update](https://git.easy.cz/devops/repo-bot-actions/update-repository/-/pipelines/new)  
   spustíme update klientova repozitáře. Opět před submitnutim vyplníme hodnotu `CONTRACT_ID` stejně jako při vytváření.
   Tato akce vytvoří v klientově repozitáři merge request s updatem do masteru.

5) V Repo-botovi  
   [RepoBot na GitLab](https://git.easy.cz/devops/repo-bot)  
   v naší update template odkomentujeme dva zakomentované řádky. A finální verzi této templaty opět commitneme a
   pushneme.
   Výsledná podoba by tedy měla vypadat nějak takto:
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
6) Vytvoření modifikačního pluginu

   Naklonujeme si klientův repozitář a vytvoříme v aktualizovaném kódu na cestě `plugins/easyproject/easy_plugins`
   modifikační plugin. Struktura a obsah souboru:
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

## Customizace vzhledu V14
   V tomto pripade staci z predesleho postupu udelat kroky 1,2. Bod 3 akorat bez zakomentovanych radek s `_theme--brand.scss`.
   A bod 6.