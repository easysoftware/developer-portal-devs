# First and setup your device 

*In this article we will show you what to do as your first steps in our company...*

---

## Workspace and workstation

At the beginning, you have to setup your workspace with generics: 

1. Encrypt your hard drive.
2. Use keyring service ([bitwarden](https://es.easyproject.com/easy_knowledge_stories/1419) is in the progress for company use).
3. Install Email client (example of setting for [thunderbird](https://es.easyproject.com/easy_knowledge_stories/909) or [outlook](https://es.easyproject.com/easy_knowledge_stories/1358).
4. Install Microsoft Teams.
5. Prepare for [backend](https://easysoftware.stoplight.io/docs/developer-portal-devs/docs/Backend_tutorials/BE-First_steps.md) and/or [frontend](https://easysoftware.stoplight.io/docs/developer-portal-devs/docs/Frontend_tutorials/FE-First_steps.md) development
6. Install [***EasyCLI***](https://git.easy.cz/internal/easy_cli) (highly recommended). Shortens the amount of clicks you have to do. For example, it automatically creates merge requests, simplifies SSH access, etc...


**We also differentiate between what system you currently use:**

### MacOS

1. First, you have to install [XCode](https://apps.apple.com/cz/app/xcode/id497799835?mt=12) from the Appstore.
2. Install ***Xcode Command Line Tools***.
```go
sudo xcode-select --install
```
3. Install version manager ***[RVM](https://rvm.io/rvm/install)*** or ***RBENV***.
```go
brew install rbenv
```
4. Install ***Ruby Bundler***.
```go
gem install bundler
```
5. Install ***Ruby-2.7.2+***. Use 2.7.2 or  for now on MacOS OR 3.0.2 with RBENV as a package manager.
> 	NOTE: Keep in mind that some newer versions of Ruby do not work flawlessly on MacOS so it is better to use an older version
6. Next we will install MySQL using command - head [here](https://flaviocopes.com/mysql-how-to-install/) to read more on how to get it running. MySQL server will then run on your workstation.
```go
brew install mysql
```


### Linux Ubuntu
The following steps should work flawlessly for any common Linux distribution (Mint, Ubuntu etc.)
1. Install [***RVM***](https://rvm.io/rvm/install) 

2.  Install ***Ruby-2.7.2+***

3. Next install Ruby bundler using the following command:
```go
gem install bundler
```
4. Install [***EasyCLI***](https://git.easy.cz/internal/easy_cli) (highly recommended). Shortens the amount of clicks you have to do. For example, it automatically creates merge requests, simplifies SSH access, etc...

5. [Install ***MySQL***](https://www.digitalocean.com/community/tutorials/how-to-install-mysql-on-ubuntu-18-04)


### Final steps
1. Generate your SSH key
```go
ssh-keygen -t rsa
```
2. Add your generated SSH key into Easy Gitlab in **Profile** > **Preferences** > **SSH Keys**
3. Download the version of the platform you want to work on. 
```go
git clone git@git.easy.cz:devel/devel.git {$TARGET_DIRECTORY}
```
4. Download database seed - you can find it here
> NOTE: the database name you use in the command will be used in all the future commands as well
5. Install the platform dependencies from the location on your workstation
```go
bundle install
```
6. Run migrations
```EasyProject
bundle exec rake easyproject:install RAILS_ENV=development
```
```Redmine
bundle exec rake db:migrate
```
7. Run the platform - it will sit at `http://localhost:3000`
```
rails s
```


By now you should have your workstation ready.