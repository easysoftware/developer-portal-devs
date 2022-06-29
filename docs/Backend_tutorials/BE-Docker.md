# Docker DEV environment
We are using Docker in our environment.
This article describe workflow based on our docker image (our compose.

## Before start
1. You need to know basics about docker (images, containers, volumes, etc.) 
Those information can be found [here](https://docs.docker.com/get-started/overview/).
2. You need be familiar with EasyProject.
3. You need some initial database.

## Build image
Basically there are two options to do it:
1. You can use official v10 image (https://git.easy.cz/easyproject_com/stable/container_registry)
OR
2. You can build your own image from git.

Images on git.easy.cz are strictly for production use, and because of this we will describe how to use own build.

### Dockerfile
From repository `Dockerfile` remove "production"-related config
```Dockerfile
ENV RAILS_DIR="${HOME}/current" \
    RAILS_DATA="${HOME}/files" \
    RAILS_LOG="${HOME}/log" \
    RUBY_VERSION="${RUBY_VERSION}" \
# [-  RAILS_ENV="production" \-]
    RAILS_MAX_WORKERS="1" \
    RAILS_MAX_THREADS="1"
# [-RUN bundle config set without 'development test'-]
```

### Docker-compose.yml
Use your own docker-compose file:

```yaml
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
### Dockerfile-easy_vue
If you are Vue.js developer, you will need node and run DEV npm (yarn). For this you need create new `Dockerfile-easy_vue` in rails root:

> Make sure that you have in .env `NODES__EASYSOFTWARE__COM` token (from https://nodes.easysoftware.com)

```Dockerfile
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
### bin/docker-entrypoint.sh
Here you need remove "production" env from puma:

```bash
  "server" | "start")
    export RAILS_LOG_TO_STDOUT=1
    bundle exec puma --dir "${RAILS_DIR}"[- -e production-] -p 3000 -C "${RAILS_DIR}/config/easy_puma.rb"
    ;;
```
### .env
Finally you need define `.env` file which contains ALL required variables in one place:

```text
DB_HOST=db
MYSQL_DATABASE=easy
MYSQL_ROOT_PASSWORD=root
MYSQL_USER=devel
MYSQL_PASSWORD=devel
RAILS_ENV=development
NODES__EASYSOFTWARE__COM=<YOUR TOKEN>
```

### More
RubyMine officially support Docker. There is a way how to use Docker container as a ruby interpreter.
https://www.jetbrains.com/help/ruby/docker.html

If you are using Visual Studio Code then you can take a look here:
https://docs.microsoft.com/en-us/learn/modules/use-docker-container-dev-env-vs-code/2-use-as-development-environment
