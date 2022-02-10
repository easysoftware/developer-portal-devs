# Backend first steps

The beginning of an awesome article...

---


## First steps as Backend developer

### Requirements

1. GIT
2. Ruby >= 2.5
3. MySQL or PostgreSQL
4. Enought space on your disk

*Recomended*

1. Imagemagick
2. Xapian
3. Wkhtmltopdf


### Just install and run

1. Clone a repository.

```ruby
git clone git@git.easy.cz:devel/devel.git TARGET_DIRECTORY
cs TARGET_DIRECTORY
```

2. Set up the database. Modify `config/database.yml` based on [Configuring a database](https://guides.rubyonrails.org/configuring.html#configuring-a-database).

3. Add your local gems. Modify the `Gemfile.local`. This file is ignored by git so gems will be available only on your machine. 

```ruby
# Typical content
# Replacing default webserver
gem 'puma'
# Gems for debugging
gem 'pry'
gem 'pry-rails'
gem 'pry-doc'

```

4. Install dependencies

```ruby
gem install bundler
bundle install
```

5. Generate secret token (*needed for security*)

```ruby
bundle exec rake generate_secret_token
```

6. Migrate Redmine

```ruby
bundle exec rake db:migrate
```

7. Migrate Easy and other plugins

```ruby
bundle exec rake easyproject:install REILS_ENV=development
```

8. Start the app

```ruby
rails s
```
> Your app should be running at http://localhost:3000.

---

## Docker DEV environment

*This text describes workflow based on our docker image (our compose).*

### Before you start

1. You need to know the basics about docker (*images, containers, volumes,...*)
2. You need to be familiar with ER/EP.
3. You need an initial database (*demo data, for example*).

### Build image

There are two options basically. You can use the official [v10 image](https://git.easy.cz/easyproject_com/stable/container_registry) **OR** build your own image from git.

Because images on www.git.easy.cz are strictly for production use, I will describe how I use my own building...

#### Dockerfile

From repository `Dockerfile` remove `"production"`-related config

```ruby
ENV RAILS_DIR="${HOME}/current" \
    RAILS_DATA="${HOME}/files" \
    RAILS_LOG="${HOME}/log" \
    RUBY_VERSION="${RUBY_VERSION}" \
# [-  RAILS_ENV="production" \-]
    RAILS_MAX_WORKERS="1" \
    RAILS_MAX_THREADS="1"
# [-RUN bundle config set without 'development test'-]
```

#### Docker-compose.yml

Use your own docker-compose file:

```ruby
# development-compose.yml
version: '3'
services:
  db:
    image: mysql:8.0
    env_file: .env
    ports:
      - 3306
    volumes:
      - db_data:/var/lib/mysql

  app:
#    image: registry.git.easy.cz/easyproject_com/stable:latest
    build:
      context: "./"
      args:
        DB: mysql
    volumes:
      - gem_cache:/usr/local/bundle/gems
      - ./plugins:/opt/easy/current/plugins
    ports:
      - 3000:3000
    environment:
      RAILS_ENV: development
      RAILS_SERVE_STATIC_FILES: 1
      RAILS_LOG_TO_STDOUT: 1
    env_file: .env
    links:
      - db
    depends_on:
      - db
    command: server
  vue_js:
    build:
      context: .
      dockerfile: Dockerfile-easy_vue
    volumes:
      - ./plugins/easy_vue:/opt/easy_vue

volumes:
  db_data:
  gem_cache:
```

#### Dockerfile-easy_vue

If you are a `Vue.js` developer, you will need node and run DEV npm (yarn). For this you need to create a new `Dockerfile-easy_vue` in rails root:

> Make sure that you have in .env `NODES__EASYSOFTWARE__COM` token from [here](https://nodes.easysoftware.com).


```ruby
FROM node:lts
ENV HOME "/opt/easy_vue"
VOLUME "/opt/easy_vue"
WORKDIR ${HOME}

ARG NODES__EASYSOFTWARE__COM
ENV NODES__EASYSOFTWARE__COM $NODES__EASYSOFTWARE__COM

RUN npm config set //nodes.easysoftware.com/:_authToken ${NODES__EASYSOFTWARE__COM}
RUN npm config set //nodes.easysoftware.com/:always-auth true
RUN npm config set @easy:registry https://nodes.easysoftware.com

COPY ./plugins/easy_vue ${HOME}
RUN npm ci

CMD npm run dev

```

#### bin/docker-entrypoint.sh

Here you need remove "`production`" env from puma:

```ruby
"server" | "start")
    export RAILS_LOG_TO_STDOUT=1
    bundle exec puma --dir "${RAILS_DIR}"[- -e production-] -p 3000 -C "${RAILS_DIR}/config/easy_puma.rb"
    ;;

```

#### .env

Finally you need define `.env` file which contains **ALL** required variables in one place:

```ruby
DB_HOST=db
MYSQL_DATABASE=easy
MYSQL_ROOT_PASSWORD=root
MYSQL_USER=devel
MYSQL_PASSWORD=devel
RAILS_ENV=development
NODES__EASYSOFTWARE__COM=<YOUR TOKEN>

```

> RubeMine offially support Docker. There is a way [how to use Docker container](https://www.jetbrains.com/help/ruby/docker.html) as a ruby interpreter.
>
>Also you can read the guide [how to use VScode with Docker](https://docs.microsoft.com/en-us/learn/modules/use-docker-container-dev-env-vs-code/2-use-as-development-environment).
>
>And last one check [Docker documentation](https://docs.docker.com/compose/compose-file/compose-file-v3)


















