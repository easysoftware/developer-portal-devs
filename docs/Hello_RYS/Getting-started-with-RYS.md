# Getting started with RYS

*In this article you will learn what is and how our new RYS plugin system works*

---

## What is RYS? 

We've introduced a new way of developing plugins for **EasyRedmine**, **Redmine** and **EasyProject**. It is almost the same as developing standard rails gems based on [Rails engine system](https://guides.rubyonrails.org/engines.html) .

RYSes are used for isolated features and allow for on the fly enabling/disabling of it's features. 

RYS is consist of:
- **RYS** (*contains Feature toggler, patch management and generators*)
- **RYSPEC** (*contains testing ryses*)
- **RYS management** (*contains RYS configuration and management*). 

<!-- theme: info -->
>***Where to find RYSes?***
>
>Each RYS has its own repository and depending on the platform configuration can be changed and installed if needed. RYS always only changes backend functionality and its controllers.
>
> Full documentation what RYSes, including source code can be found in [github](https://github.com/easysoftware/rys).

--- 
### How RYS works?

It is important to understand how RYSes are built inside so let's look at the way they are structured. The overall structure is driven by what it does, whether it adds new functions or changes existing functionality.

In case of the RYS **adds new functionality** then their structure follows the common Rails architecture and adds new classes into lib folder.

In case of RYS **changes or enhances functionality** then the structure follows this logic:
1. Into the `patch` folder go all alterations to existing files that affect any platform files.
2. Following the same logic, if new RYS alters functionality of another RYS then new code files go in the `patch` folder and the subfolder of the changed RYS.

Each RYS should contain information about its author, version, short description about its functionality and under what license it can be used. All this is saved in its very own `.gemspec` file.

---
### How to install RYS?

1. As your first step you have to **add to your gem file** (Gemfile, gemfile.local, gems.rb,...)

```ruby
git_source(:github) {|name| "https://github.com/#{name}.git" }
group :default, :rys do
  gem 'rys', github: 'easysoftware/rys', branch: 'master'
end
```
2. Next  **install your gem file**
```ruby
  bundle install
```
3. As a next step **add additionals**
```ruby
 rails generate rys:redmine:plugin NAME
```
<!-- theme: info -->
>This will generate a new Redmine plugin (plugins/NAME) contains basic plugins (`rys-bundler`, `ryspec`, ...) and generated skeleton for adding more ryses (`plugins/NAME/Gemfile`).

4. And a last step is **generate RYS** with name *NAME* and follow instruction. 

```ruby
 rails generate rys:plugin NAME
```

And that is it. RYS platform is fully installed and ready to use.

---
## RYS Builder
### What is RYS Builder?

**TODO NA DOPLNĚNÍ** 

---

### How to install RYS Builder?

If you want to download RYS Builder copy/simlink all rys dependecies into your project directory. 

1. First of all **copy gems** into `plugins/easysoftware/gems` 

```ruby
bundle exec rake easybundler:build
```

2. **Link gems** into `plugins/easysoftware/gems`

```ruby
bundle exec rake easybundler:build_local
```

3. **Revent gems** from `plugins/easysoftware/gems`

```ruby
bundle exec rake easybundler:revert
```

4. **Revent gems** from plugins/easysoftware/local

```ruby
bundle exec rake easybundler:revert_local
```

And that is all Now you call sucessfully use RYS Builder.

---



## How to start developing with RYS? *(Best practice one of our developers)*

As a first step you will need **Redmine** or **EasyRedmine(ER)**. 

Because RYSes are designed for developing (Easy) Redmine plugins, you need to have it set up first.

> [EasyRedmine platform](https://git.easy.cz/devel/devel/tree/devel) version 04.00 and up includes the rys gem.

#### Generate your engine

We have prepared a simple generator of RYS engines.

1. Add the `rys` gem if not already included

2. Run rails generator
   
   ```ruby
   rails generate rys:plugin NAME
   ```

#### Move generated code to work directory

All engines will have separate GIT repository. It's better to store engines outside of ER git repository.

> Use `--path` generator option.

#### Dummy application

Rails 5 engines are best developed with a dummy application, which doesn't contain a lot of unnecessary code. In RubyMine you only need to open the RYS source, so its fast and easy. 

So you take our `devel` repository, clone it as `dummy` and remove **ALL unnecessary plugins**. It can be found [here](https://git.easy.cz/easyproject-client/dummy)
- `devel` corresponds with [devel/devel.git#devel](https://git.easy.cz/devel/devel/tree/devel)
- `master` with [devel/devel.git#master](https://git.easy.cz/devel/devel/tree/master)

You symlink this dummy to `test/dummy` in the RYS engine.

### Running tests

1. Run
```
bundle install
```
2. Make sure you have configured `config/database.yml` in your dummy.
3. Run migration
```
app:db:create app:db:migrate
```
4. Build Easy Project
```
bundle exec rake app:easyproject:install
```
5. Run rspec
```
rspec
```

#### Example of geting dummy app and running tests


1. Get EasyRedmine
   
   ```ruby
   git clone devel -b devel devel
   ```

2. Setup EasyRedmine (database.yml)
   
   ```ruby
   bundle install --without rmagick xapian
   ```

3. Generate RYS
   
   ```bash
   rails g rys:plugin pink_unicorn --path ../
   ```

4. Prepare dummy
   
   ```ruby
   git clone git@git.easy.cz:easyproject-client/dummy.git -b devel dummy
   vim config/database.yml
   ```

5. Link dummy to RYS
   
   ```ruby
   cd ../pink_unicorn
   mkdir test
   ```
   
6. test folder is in `.gitignore` by default
  
   ```ruby
   ln -s [~/projects/ or absolute path]dummy test/dummy
   ```

7. Put it together
   
   ```ruby
   bundle install --without rmagick xapian
   rake app:db:create app:db:migrate
   rake app:easyproject:install # this work from ER 05.00
   ```

8. Ready to use

RYS is ready. Open RubyMine and write some code! After that run `rspec`.

```ruby
.*

Pending: (Failures listed here are expected and do not affect your suite's status)

  1) Model test Fail something
     # No reason given
     Failure/Error: expect(true).to eq false

       expected: false
            got: true

       (compared using ==)
     # ./spec/models/model_spec.rb:8:in `block (2 levels) in '

Finished in 0.50731 seconds (files took 2.92 seconds to load)
2 examples, 0 failures, 1 pending
```










