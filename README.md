# Iniciando um novo projeto utilizando o docker-compose

1. Criando os arquivos

```
$ touch Dockerfile docker-compose.yml Procfile.dev
```

* Dockerfile

```
FROM ruby:2.6.6

RUN apt-get update -qq && apt-get install -y build-essential nodejs tzdata libpq-dev \
  postgresql-client && rm -rf /var/lib/apt/lists/*

RUN curl -sL https://deb.nodesource.com/setup_12.x | bash -
RUN apt-get install -y nodejs

RUN npm install -g yarn
RUN yarn install --check-files

COPY . /usr/src/app
WORKDIR /usr/src/app
```

* docker-compose.yml

```
version: '3.6'

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
    volumes:
      - ./tmp/db:/var/lib/postgresql/data
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
    command: bundle exec sidekiq --config ./config/sidekiq.yml
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
worker: bundle exec sidekiq --config ./config/sidekiq.yml
```

2. Instalar o Rails 6

```
$ sudo docker-compose run --rm web gem install rails -v 6.0.3
```

3. Criar o projeto

```
$ docker-compose run --rm web rails new . -T --force --database=postgresql
```

4. Adicionar a gem foreman em seu `Gemfile`

```
gem 'foreman'

# execute o bundle em seguida:
$ docker-compose run --rm web bundle
```

5. Trocando permissão dos arquivos

```
$ sudo chown -R $USER:$USER .
```

6. Instalando react (opcional)

```
$ docker-compose run --rm web rails webpacker:install:react

<%= javascript_pack_tag 'hello_react' %>

# Editar a configuração host do webpacker `config/webpacker.yml`

dev_server:
  host: 0.0.0.0
```

7. Editar o arquivo `config/environments/development.rb`

```
# Add to whitelist the '172.18.0.1' network space in the Web Console config.
config.web_console.whitelisted_ips = ['192.168.0.0/16', '172.0.0.0/8']
```

8. Configurando o banco de dados `database.yml`

```
development: &default
  adapter: postgresql
  database: my_app_development
  encoding: unicode
  host: <%= ENV.fetch('DB_HOST') %>
  username: postgres
  password:
  pool: <%= ENV.fetch('RAILS_MAX_THREADS') { 5 } %>

test:
  <<: *default
  database: my_app_test

production:
  <<: *default
  database: my_app_production
```

9. Criando o banco de dados

```
$ docker-compose run --rm web rails db:create
```

10. Acessando banco de dados

```
$ docker-compose run --rm db psql -h db -U postgres

Ou:

$ docker-compose run --rm db psql -d postgres://postgres@db/my_app_development
```

11. Subindo seu servidor

```
$ docker-compose up web
```

12. Acessar o endereço `localhost:3000`


[Referência](https://gist.github.com/erdostom/5dd400cbba17d44b52b2f74b038fcb85)
