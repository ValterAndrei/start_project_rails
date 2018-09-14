# Rubocop

1. Gerando o arquivo `.rubocop_todo.yml`

```
rubocop --auto-gen-config
```

2. Desabilitando todas as verificações `.rubocop_todo.yml`

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

3. Desabilitando verificações específicas `.rubocop_todo.yml`

```
Style/TernaryParentheses:
  Exclude:
    - 'app/services/measurement_table.rb'

Style/FrozenStringLiteralComment:
  Exclude:
    - 'app/**/*'
    - 'bin/**/*'
```

4. Início do arquivo

```
# rubocop:disable all
# rubocop:disable Metrics/LineLength, Style/StringLiterals
```

5. Na frente do método

```
def very_long_method # rubocop:disable Metrics/MethodLength
  ...
end
```


[Fonte](https://medium.freecodecamp.org/rubocop-enable-disable-and-configure-linter-checks-for-your-ruby-code-475fbf11046a)
