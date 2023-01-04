# Initial setup

*In this article we will show you what to do as your first steps in Easy Software.*

---

## Workspace and workstation

At the beginning, you have to setup your workspace with generics:

- Prepare for [backend](https://easysoftware.stoplight.io/docs/developer-portal-devs/docs/Backend_tutorials/BE-First_steps.md) and/or [frontend](https://easysoftware.stoplight.io/docs/developer-portal-devs/docs/Frontend_tutorials/FE-First_steps.md) development
- Install [***EasyCLI***](https://git.easy.cz/internal/easy_cli) (highly recommended). Shortens the amount of clicks you have to do. For example, it automatically creates merge requests, simplifies SSH access, etc...

**We also differentiate between what system you currently use:**

### MacOS

1. First, you have to install [XCode](https://apps.apple.com/cz/app/xcode/id497799835?mt=12) from the Appstore.
2. Install ***Xcode Command Line Tools***.

```
sudo xcode-select --install
```

3. Install version manager ***[RVM](https://rvm.io/rvm/install)*** or ***RBENV***.

```
brew install rbenv
```

4. Install ***Ruby Bundler***.

```
gem install bundler
```

5. Install ***Ruby-3.1.2***. Use 3.1.2 for now on MacOS with RBENV as a package manager.

> 	NOTE: Keep in mind that some newer versions of Ruby do not work flawlessly on MacOS so it is better to use an older version

6. Next we will install MySQL using command - head [here](https://flaviocopes.com/mysql-how-to-install/) to read more on how to get it running. MySQL server will then run on your workstation.

```
brew install mysql
```

<!-- theme: warning -->
> It must be [Percona](https://www.percona.com/doc/percona-server/8.0/index.html)

#### Apple M1 processors

<!-- theme: info -->
> Please be aware that on new ARM processors you may encounter issues during installation process.
> To be able to successfully set up everything you may need Rosetta terminal (see [here](https://betterprogramming.pub/5-things-i-have-learned-when-using-the-m1-chip-macbook-air-a77f93c50381#5a64)).

<!-- theme: info -->
> To successfully install MySQL gem on your M1 you need to follow those two steps:
>1. Make sure mysql, openssl and zstd are installed on Mac via Homebrew.
> ```
> brew install mysql openssl zstd 
> ```
>2. Install mysql2 gem.
> ```
> gem install mysql2 -v '0.5.3' -- --with-mysql-config=$(brew --prefix mysql)/bin/mysql_config --with-ldflags="-L$(brew --prefix zstd)/lib -L$(brew --prefix openssl)/lib" --with-cppflags=-I$(brew --prefix openssl)/include
>```

### Linux Ubuntu

The following steps should work flawlessly for any common Linux distribution (Mint, Ubuntu etc.)

1. Install [***RVM***](https://rvm.io/rvm/install)

2. Install ***Ruby-3.1.2***

3. Next install Ruby bundler using the following command:

```
gem install bundler
```

4. Install [***EasyCLI***](https://git.easy.cz/internal/easy_cli) (highly recommended). Shortens the amount of clicks you have to do. For example, it automatically creates merge requests, simplifies SSH access, etc...

5. Install [***MySQL***](https://www.digitalocean.com/community/tutorials/how-to-install-mysql-on-ubuntu-22-04) \
   ⚠️ Pay attention to the warning in "Step 2 — Configuring MySQL" section of the guide. ⚠️

### Windows using WSL(Windows Subsystem for Linux)

1. Use the latest Ubuntu LTS version in your WSL and continue inside it's terminal

2. Install [***RVM***](https://rvm.io/rvm/install)

3. Install ***Ruby-3.1.2***

4. Next install Ruby bundler using the following command:

```
gem install bundler
```

4. Install [***EasyCLI***](https://git.easy.cz/internal/easy_cli) (highly recommended). Shortens the amount of clicks you have to do. For example, it automatically creates merge requests, simplifies SSH access, etc...

5. Install [***MySQL***](https://learn.microsoft.com/en-us/windows/wsl/tutorials/wsl-database) \
   ⚠️ Before running `mysql_secure_installation` open up the MySQL prompt with `sudo mysql` and run:
   ```
   ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
   ```
   It sets authentication method and a password(feel free to change it). More info at https://www.digitalocean.com/community/tutorials/how-to-install-mysql-on-ubuntu-22-04#step-2-configuring-mysql

### Docker

⚠️ Under construction ⚠️

### Final steps

1. Generate your SSH key

```
ssh-keygen -t rsa
```

2. Add your generated SSH key into Easy Gitlab in **Profile** > **Preferences** > **SSH Keys**
3. Download the version of the platform you want to work on.

```
git clone git@git.easy.cz:devel/devel.git {$TARGET_DIRECTORY}
```

4. Download database seed - you can find it here

> NOTE: the database name you use in the command will be used in all the future commands as well

5. Install the platform dependencies from the location on your workstation

```
bundle install
```

6. Run migrations

```Easy Project
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
