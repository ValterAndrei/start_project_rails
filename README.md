# Iniciando um novo projeto utilizando o Docker

1. Remover git init

```
$ rm -rf .git
```

2. Instalar ambiente

```
$ docker-compose build
```

3. Construir o projeto

```
$ docker-compose run --rm web rails new . --force --database=postgresql --webpack --skip-coffee
$ docker-compose build
$ docker-compose run --rm web rails webpacker:install
$ docker-compose run --rm web rails webpacker:install:vue
```

4. Se for linux executar este comando

```
$ sudo chown -R $USER:$USER .
```

5. Editar o arquivo `config/database.yml`

```
default: &default
  adapter: postgresql
  encoding: unicode
  host: db
  username: postgres
  password:
  pool: 5

development:
  <<: *default
  database: myapp_development


test:
  <<: *default
  database: myapp_test
```

6. Criar banco de dados

```
$ docker-compose run --rm web rails db:create
```

7. Editar o arquivo `config/webpacker.yml` (linha 37)

```
dev_server:
  host: 0.0.0.0 # or the docker IP
```

8. Editar o arquivo `config/environments/development.rb` (inserir no inicio do código)

```
# Make javascript_pack_tag load assets from webpack-dev-server.
config.x.webpacker[:dev_server_host] = "http://localhost:8080"
```

9. Inserir a tag na View em que desejar:

```
<%= javascript_pack_tag 'hello_vue' %>
```

10. Subir aplicação

```
$ docker-compose up web
```

11. Acessar a página localhost

`
http://localhost:3000
`

