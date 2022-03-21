### Starting a new Rails project using docker-compose

1. Create files

```
$ touch Dockerfile docker-compose.yml Procfile.dev
```

* Dockerfile

```dockerfile
FROM ruby:3.1

RUN apt-get update -qq && apt-get install -y build-essential tzdata libpq-dev \
  postgresql-client && rm -rf /var/lib/apt/lists/*

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
$ docker-compose run --rm web gem install rails -v 7.0.2.2
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

7. Install front-end framework [vue] - *optional*

```
$ docker-compose run --rm web rails webpacker:install:vue

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
  host: db
  database: app_development

test:
  <<: *default
  host: db
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

# or:

$ docker-compose run --rm db psql -d postgres://postgres@db/app_development
```

11. Up server

```
$ docker-compose up web
```

> `localhost:3000`

### Deploying at Heroku
- [Tutorial](https://devcenter.heroku.com/articles/getting-started-with-rails6)
