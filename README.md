# Trabalho sobre Clean Code - UNISEP
Esse trabalho foi realizado com o intuito de aplicar em Ruby os conseitos apresentados nos capitulos 7 e 8 do livro "Clean Code" de Martin
Robert C.

Mostraremos alguns exemplos de boas práticas apresentadas no livro. Lembrando que o livro utiliza a linguagem Java como base, então criamos nossos próximos exemplos ataptados para a linguagem Ruby.

## Tratamento de Erros - Capitulo 7

"Um jeito de escrever código limpo e robusto, que trata erros com elegancia e estilo." - Frase do livro

O objetivo desse capítulo é apresentar algumas boas práticas no tratamento de erros dos seus códigos.

### 1. Usar ```begin rescue``` no lugar de if e else

Essa é a primeira boa pática, evitar o uso de if e else para tratamento de erros. Pois em situações complexas, o código fica extremamente confuso e nada clean. Por isso é importante conhecer o recurso de tratamento de erros do Ruby, o ```begin rescue```.

:warning: Ruim: Usando ```if e else``` para tratar erros
```
if (x != nil)
  x.length
  if (y != nil)
    y.length
  else
    puts "Houve um erro"
  end
else
  puts "Houve um erro"
end
```

:heavy_check_mark: Bom: Utilizando o ```begin rescue``` para tratar erros

```
begin
  x.length
  y.length 
rescue
  puts "Houve um erro"
end
```

### 2. Sempre criar mensagens de erro informativas

É importante criar mensagens de erro informativas e com um contexto bem definido de onde o erro ocorreu e por que ocorreu.

Considera a seguinte função:
(ela dispara um erro caso a divisão seja por zero)
```
def dividir(x, y)
  raise StandardError.new "Divisão por zero" if y == 0
  return x / y
end
```

:warning: Ruim:
Mensagem sem contexto:

```
begin
  dividir(2, 0)
rescue
  puts "Deu erro"
end
```
ouput:
```
Deu erro
```

:heavy_check_mark: Bom:
Mensagem com contexto, e informativa a respeito do erro:

```
begin
  dividir(2, 0)
rescue StandardError => e
  puts "Erro ao dividir: #{e.message}"
end
```
output:
```
Erro ao dividir: Divisão por zero
```

### 3. Abstrair erros em uma classe separada

Essa boa prática se aplica principalmente na utilização de APIs de terceiros. Consiste-se em isolar a lógica de tratamento de erros em uma classe, dessa forma o seu código não fica completamente dependendo do fluxo definido pela API de terceiros, você mesmo consegue adaptar o tratamento de erros e padroniza-lo com a sua aplicação.

> O exemplo abaixo utiliza o IO do Ruby para abrir um arquivo, seguimos a documentação para ver os possíveis retornos de erro

:warning: Ruim: Tratar diversos erros diretamente, sem isolar a lógica

```
begin
  IO.sysopen('/dev/null')
rescue Errno::ENOENT
  puts "Arquivo não encontrado."
rescue Errno::EACCES
  puts "Sem permissão para acessar arquivo."
rescue Errno::ENOMEM
  puts "Sem memória RAM livre."
rescue Errno::EBUSY
  puts "Arquivo está sendo utilizado por outro recurso."
end
```

:heavy_check_mark: Bom: Isolar o tratamento de erros em uma classe

```
arquivo = Arquivo.new

begin
  arquivo.abrir
rescue StandardError => e
  puts "Erro ao abrir arquivo: #{e.mensagem}"
end
```

Classe com os erros tratados:
```
class Arquivo
  def abrir
    begin
      IO.sysopen('/dev/null')
    rescue Errno::ENOENT
      raise StandardError.new "Arquivo não encontrado."
    rescue Errno::EACCES
      raise StandardError.new "Sem permissão para acessar arquivo."
    rescue Errno::ENOMEM
      raise StandardError.new "Sem memória RAM livre."
    rescue Errno::EBUSY
      raise StandardError.new "Arquivo está sendo utilizado por outro recurso."
    end
  end
end
```
Note que nessa classe é possível padronizar todos os erros, dando uma flexibilidade muito maior no tratamento, e deixando ele moldado para os padrões da sua aplicação.


## Limites - Capítulo 8
O objetivo desse capítulo é apresentar boas práticas para manter limpos os limetes de nosso software, principalmente durante a utilização de códigos de terceiros.

Para esse capítulo, traremos apenas um exemplo, que demonstra como podemos melhorar a utilização de códigos tercerizados.

Utilizaremos a gem ```logger``` para a demonstração. (https://github.com/ruby/logger)

:warning: Caso ruim:
```
require 'logger'

log = Logger.new(STDOUT)
log.debug("hello")

log.info("Program finished")

log_error = Logger.new(STDERR)
log.error("erro")
```
Explicação: O problema de seguir essa implementação, é que nossa aplicação fica completamente dependente da forma como o código está implementado na gem, caso futuramente a gem sofra alterações (por exemplo, mudanças de nomenclatura) nosso código quebraria em vários lugares, e teriamos que ajustá-los um por um.

:heavy_check_mark: Caso bom:
```
require 'logger'

class Log
  def initialize
    @log = nil
  end

  def debug(string)
    @log = Logger.new(STDOUT)
    @log.debug(string)
  end

  def info(string)
    @log = Logger.new(STDOUT)
    @log.info(string)
  end

  def error(string)
    @log = Logger.new(STDERR)
    @log.error(string)
  end
end

log = Log.new

log.debug("valor da variavel x é 2")
log.info("Sistema em Execução")
log.error("Não foi possível conectar com o banco de dados")
```
Explicação: Esse é o caso ideal, pois criamos a classe Log, com a utilização da gem ```logger``` encapsulada nela. Isso é bom porque limitamos a forma como dependemos do código tercerizado, pois caso a gem sofra alguma alteração, teremos apenas um único lugar para corrigir, e isso refletirá em toda a nossa aplicação. Outro detalhe é que ganhamos flexibilidade, pois podemos ajustar a forma como interagimos com a gem.

Fim.

Autores: André Prolo e Marco Antonio
