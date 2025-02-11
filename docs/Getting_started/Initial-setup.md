# Initial Setup

*This guide walks you through the first steps before you start coding.*

---

## macOS Setup

1. Install ***Xcode Command Line Tools***:
   ```sh
   sudo xcode-select --install
   ```
2. Install [***MySQL***](https://flaviocopes.com/mysql-how-to-install/).
3. Continue to [Next Steps](#next-steps).

### Apple Silicon (All M-Series Processors)

> **Note:** Installing the MySQL gem on Apple Silicon Macs requires additional steps:

1. Install dependencies via Homebrew:
   ```sh
   brew install mysql openssl zstd
   ```
2. Install the `mysql2` gem with the following command:
   ```sh
   gem install mysql2 -v '0.5.6' \
     -- --with-mysql-config=$(brew --prefix mysql)/bin/mysql_config \
     --with-ldflags="-L$(brew --prefix zstd)/lib -L$(brew --prefix openssl)/lib" \
     --with-cppflags="-I$(brew --prefix openssl)/include"
   ```

---

## Linux (Ubuntu) Setup

These steps work for most Debian-based distributions (Ubuntu, Mint, etc.).

1. Install [***MySQL***](https://www.digitalocean.com/community/tutorials/how-to-install-mysql-on-ubuntu-22-04).  
   ⚠️ **Important:** Review the "Step 2 — Configuring MySQL" section in the guide.

2. Install the MySQL client library:
   ```sh
   sudo apt-get install libmysqlclient-dev
   ```
3. Continue to [Next Steps](#next-steps).

---

## Windows (WSL - Windows Subsystem for Linux)

1. Use the latest Ubuntu LTS version in WSL and open its terminal.
2. Install [***MySQL***](https://learn.microsoft.com/en-us/windows/wsl/tutorials/wsl-database#install-mysql).  
   ⚠️ Before running `mysql_secure_installation`, configure MySQL authentication:
   ```sh
   ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
   ```
   > This sets the authentication method and password (feel free to modify it). More details are available in the
   > [DigitalOcean guide](https://www.digitalocean.com/community/tutorials/how-to-install-mysql-on-ubuntu-22-04#step-2-configuring-mysql).

3. Install the MySQL client library:
   ```sh
   sudo apt-get install libmysqlclient-dev
   ```
4. Continue to [Next Steps](#next-steps).
5. *(Optional)* Configure Ruby interpreter for RubyMine: [Guide](https://dev.to/ericksk/rubymine-add-a-ruby-interpreter-using-wsl-553p).

---

## 🐳 Docker

> **The easiest way to set up your development environment!**  
> Avoid dependency issues and manual installations—just run Docker and start coding.

To get started, follow the official guide here:  
📖 [Docker Setup Guide](https://git.easy.cz/devel/devel/-/tree/next/bugs/docker)

---

## Next Steps

1. Install a Ruby version manager: [***rbenv***](https://github.com/rbenv/rbenv#installation) or [***RVM***](https://rvm.io/rvm/install).
2. Install **Ruby 3.3.7**.
3. Generate an SSH key:
   ```sh
   ssh-keygen -t ed25519
   ```
4. Add your SSH key to Easy GitLab: **Profile** > **Preferences** > **SSH Keys**.
5. Clone the platform repository:
   ```sh
   git clone git@git.easy.cz:devel/devel.git
   ```
6. Navigate to the cloned directory:
   ```sh
   cd devel
   ```
7. Install ***Bundler***:
   ```sh
   gem install bundler
   ```
8. Install platform dependencies:
   ```sh
   bundle install
   ```
9. **Set up the database:**
    - Copy `config/database.yml.example` and rename it to `config/database.yml`.
    - Update database credentials in `config/database.yml` ([Configuration Guide](https://guides.rubyonrails.org/configuring.html#configuring-a-database)).
    - **Prepare your development database:**
        - Using an SQL dump:
          ```sh
          gunzip -c PATH_TO_DUMP | mysql MY_DB_NAME
          ```
          *(Download a sample dump: [demo_database_dump](https://github.com/easysoftware/developer-portal-devs/raw/unify_and_improve_be_setup/files/demo_dump_20232704.sql.gz).)*
        - OR create a clean database:
          ```sh
          bundle exec rake db:create RAILS_ENV=development
          ```
          > **WSL Users:** Use `bundle exec rake db:create RAILS_ENV=development NAME=''` instead.
10. **Run database migrations:**
    ```sh
    bundle exec rake db:migrate RAILS_ENV=development
    ```
    > **WSL Users:** Use `bundle exec rake db:migrate RAILS_ENV=development NAME=''` instead.**
11. **Run EasyInstall script:**
    ```sh
    bundle exec rake easy:install RAILS_ENV=development
    ```
    > **WSL Users:** Use `bundle exec rake easy:install RAILS_ENV=development NAME=''` instead.

12. Start the Rails server (available at `http://localhost:3000`):
    ```sh
    rails s
    ```

13. (**Bonus**) Install [***EasyCLI***](https://git.easy.cz/internal/easy_cli) *(highly recommended)*.
    > It automates tasks like creating merge requests and simplifying SSH access.
---

### 🚂 Choo-Choo! Your Rails Server is Leaving the Station! 


Your development environment is now ready!
