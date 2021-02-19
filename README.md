### Starting a new Rails project using docker-compose

1. Create files

```
$ touch Dockerfile docker-compose.yml Procfile.dev
```

* Dockerfile

```dockerfile
FROM ruby:3.0.0

RUN apt-get update -qq && apt-get install -y build-essential nodejs tzdata libpq-dev \
  postgresql-client && rm -rf /var/lib/apt/lists/*

RUN curl -sL https://deb.nodesource.com/setup_14.x | bash -
RUN apt-get install -y nodejs

RUN npm install -g yarn
RUN yarn install --check-files

COPY . /usr/src/app
WORKDIR /usr/src/app
```

* docker-compose.yml

```yml
version: '3.7'

volumes:
  gems-app:

services:
  web:
    tty: true
    stdin_open: true
    build: .
    environment:
      DB_HOST: 'db'
      REDIS_URL: 'redis://redis:6379/12'
    command: foreman start -f Procfile.dev
    volumes:
      - ./:/usr/src/app
      - gems-app:/usr/local/bundle
    ports:
      - '3000:3000'
      - '3035:3035'
    depends_on:
      - db
      - redis

  db:
    image: postgres
    environment:
      POSTGRES_HOST_AUTH_METHOD: 'trust'

  redis:
    image: redis
    command: redis-server
    ports:
      - '6379:6379'

  sidekiq:
    build: .
    environment:
      DB_HOST: 'db'
      REDIS_URL: 'redis://redis:6379/12'
    command: bundle exec sidekiq
    volumes:
      - ./:/usr/src/app
      - gems-app:/usr/local/bundle
    depends_on:
      - db
      - redis
```

* Procfile.dev

```
web: bash -c "rm -f tmp/pids/server.pid && bundle exec rails s -p 3000 -b '0.0.0.0'"
webpacker: ./bin/webpack-dev-server
worker: bundle exec sidekiq
```

2. Install Rails

```
$ docker-compose run --rm web gem install rails -v 6.1.3
```

3. Build project

```
$ docker-compose run --rm web rails new . -T --force --database=postgresql
```

4. Changing file permission

```
$ sudo chown -R $USER:$USER .
```

5. Add foreman gem to your `Gemfile`

```
gem 'foreman'
```

6. Run *bundle*
```
$ docker-compose run --rm web bundle
```

7. Install front-end framework [react] - *optional*

```
$ docker-compose run --rm web rails webpacker:install:react

<%= javascript_pack_tag 'hello_react' %>

# Edit the host configuration of the webpacker file `config/webpacker.yml`

dev_server:
  host: 0.0.0.0
```

8. Configure database `database.yml`

```yml
default: &default
  adapter: postgresql
  encoding: unicode
  username: postgres
  pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>

development:
  <<: *default
  host: <%= ENV['DB_HOST'] %>
  database: app_development

test:
  <<: *default
  host: <%= ENV['DB_HOST'] %>
  database: app_test

production:
  <<: *default
  database: app_production
  username: app
  password: <%= ENV['APP_DATABASE_PASSWORD'] %>
```

9. Create database

```
$ docker-compose run --rm web rails db:create
```

10. Access database - *optional*

```
$ docker-compose run --rm db psql -h db -U postgres

# Or:

$ docker-compose run --rm db psql -d postgres://postgres@db/app_development
```

11. Up server

```
$ docker-compose up web
```

> `localhost:3000`


[Reference](https://gist.github.com/erdostom/5dd400cbba17d44b52b2f74b038fcb85)
