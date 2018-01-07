# Iniciando um novo projeto utilizando o Docker

1. Remover git init

```
$ rm -rf .git
```

2. Instalar ambiente

```
$ docker-compose build
```

3. Instalar gems

```
$ docker-compose run --rm -u root web bash -c "mkdir -p /bundle/vendor && chown -R railsuser /bundle/vendor"
$ docker-compose run --rm web bundle install --path /bundle/vendor111

```

4. Construir o projeto

```
$ docker-compose run --rm web bundle exec rails new . -T --force --database=postgresql --webpack --skip-coffee --skip-bundle

# optional
$ docker-compose run --rm web bundle exec rails webpacker:install:vue
```

5. Se for linux executar este comando (caso precise)

```
$ sudo chown -R $USER:$USER .
```

6. Editar o arquivo `config/database.yml`

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

7. Criar banco de dados

```
$ docker-compose run --rm web bundle exec rails db:setup
```

8. Editar o arquivo `config/webpacker.yml`

```
dev_server:
  host: 0.0.0.0
```

9. Editar o arquivo `config/environments/development.rb` (inserir no inicio do código)

```
# Make javascript_pack_tag load assets from webpack-dev-server.
config.x.webpacker[:dev_server_host] = "http://localhost:8080"

# Add to whitelist the '172.18.0.1' network space in the Web Console config.
config.web_console.whitelisted_ips = '172.18.0.1'
```

10. Inserir a tag na View em que desejar:

```
<%= javascript_pack_tag 'hello_vue' %>
```

11. Subir aplicação

```
$ docker-compose up web
# or
$ docker-compose run --rm web bundle exec bash
```

12. Acessar a página localhost

`
http://localhost:3000
`


# Fonte

[blog 1](https://blog.codeminer42.com/zero-to-up-and-running-a-rails-project-only-using-docker-20467e15f1be)
<br />
[blog 2](https://hovancik.net/blog/2017/07/02/creating-new-rails-and-vue-js-app-with-docker/)
