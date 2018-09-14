# Rubocop


1. Instalando gem rubocop

```
group :development do
  gem 'rubocop', require: false
end
```

2. Gerando o arquivo de configuração `.rubocop_todo.yml`

```
rubocop --auto-gen-config
```

3. Desabilitando todas as verificações `.rubocop_todo.yml`

```
AllCops:
  Exclude:
    - 'app/**/*'
    - 'spec/**/*'
    - 'bin/**/*'
    - 'lib/**/*'
    - '**/*.rb'
    - '**/Gemfile'
    - 'node_modules/**/*'
```

4. Desabilitando verificações específicas `.rubocop_todo.yml`

```
Style/TernaryParentheses:
  Exclude:
    - 'app/services/measurement_table.rb'

Style/FrozenStringLiteralComment:
  Exclude:
    - 'app/**/*'
    - 'bin/**/*'
```

5. Desabilitando verificações dentro do arquivo

```
# rubocop:disable all
# rubocop:disable Metrics/LineLength, Style/StringLiterals
```

6. Desabilitando verificações na frente do método

```
def very_long_method # rubocop:disable Metrics/MethodLength
  ...
end
```


[Fonte](https://medium.freecodecamp.org/rubocop-enable-disable-and-configure-linter-checks-for-your-ruby-code-475fbf11046a)
