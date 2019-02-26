# Iniciando um novo projeto utilizando o Docker

1. Remover git init

```
$ rm -rf .git
```

2. Instalar ambiente

```
$ docker-compose build
```

3. Instalar o Rails

```
$ docker-compose run --rm web bash
$ gem install rails -v 5.2.0
```

4. Construir o projeto

```
$ docker-compose run --rm web rails new . -T --force --database=postgresql --webpack --skip-coffee

# optional
$ docker-compose run --rm web rails webpacker:install
$ docker-compose run --rm web rails webpacker:install:react
```

5. Editar o arquivo `config/database.yml`

```
development: &default
  adapter: postgresql
  database: myapp_development
  encoding: unicode
  host: db
  username: postgres
  password:
  pool: 5

test:
  <<: *default
  database: myapp_test

production:
  <<: *default
  database: myapp_production
```

6. Criar banco de dados

```
$ docker-compose run --rm web rails db:create
```

7. Instalar a gem foreman `Gemfile`

```
gem 'foreman'
```

8. Editar o arquivo `config/webpacker.yml`

```
dev_server:
  host: 0.0.0.0
```

9. Editar o arquivo `config/environments/development.rb` (inserir no inicio do código)

```
# Add to whitelist the '172.18.0.1' network space in the Web Console config.
config.web_console.whitelisted_ips = ['192.168.0.0/16', '172.0.0.0/8']

# Level of logs

config.log_level = :warn
# config.log_level = :debug
```

10. Criar home page

```
rails generate controller home index  --no-helper --no-assets --no-controller-specs --no-view-specs
```

11. Inserir a tag no arquivo `app/views/home/index.html.erb`

```
<%= javascript_pack_tag 'hello_react' %>
```

12. Subir aplicação

```
$ docker-compose up web

# bash:
$ docker-compose run --rm web bash
```

13. Acessar a página localhost

http://localhost:3000
