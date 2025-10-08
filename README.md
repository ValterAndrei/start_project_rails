## Iniciando um novo projeto Rails utlizando apenas Docker

### Referência: [rails-new](https://github.com/rails/rails-new)


1. Baixar arquivo
```bash
curl -L -o rails-new.tar.gz \
  "https://github.com/rails/rails-new/releases/download/v0.5.0/rails-new-x86_64-unknown-linux-gnu.tar.gz"
```

2. Descompactar e instalar
```bash
tar -xzf rails-new.tar.gz
# isso deve criar um executável chamado `rails-new` no diretório atual

sudo mv rails-new /usr/local/bin/rails-new
sudo chmod +x /usr/local/bin/rails-new
```

3. Teste
```bash
# ver ajuda
rails-new --help

# gerar um app exemplo
rails-new playground \
  --api \
  --database=postgresql \
  --skip-action-mailbox \
  --skip-action-text \
  --skip-active-storage \
  --skip-sprockets \
  --skip-javascript \
  --skip-hotwire \
  --skip-system-test
```
