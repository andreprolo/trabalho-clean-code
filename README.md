# Trabalho sobre Clean Code - UNISEP
Esse trabalho foi realizado com o intuito de aplicar em Ruby os conseitos apresentados nos capitulos 7 e 8 do livro Clean Code de Robert C. Martin

Autores: André Prolo e Marco Antônio

Mostraremos alguns exemplos de boas práticas apresentadas no livro. Lembrando que o livro utiliza a linguagem Java como base, então criamos nossos próximos exemplos ataptados para a linguagem Ruby.

## Tratamento de Erros - Capitulo 7

"Um jeito de escrever código limpo e robusto, que trata erros com elegancia e estilo." - Frase do livro

O objetivo desse capitulo é apresentar algumas boas práticas no tratamento de erros dos seus códigos.

### 1. Usar ```begin rescue``` no lugar de if e else

Essa é a primeira boa pática, evitar o uso de if e else para tratamento de erros. Pois em situações complexas, o código fica extramamente confuso e nada clean. Por isso é importante conhecer o recurso de tratamento de erros do Ruby, o ```begin rescue```.

Ruim: 
Usando if e else para tratar erros
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

Bom:
Utilizar o begin rescue para tratar o possível erro

```
begin
  x.length
  y.length 
rescue
  puts "Houve um erro"
end
```

### 2. Sempre criar mensagens de erro informativas

Considera a seguinte função:
```
def dividir(x, y)
  raise StandardError.new "Divisão por zero" if y == 0
  return x / y
end
```

Ruim:
Mensagem sem contexto:

```
begin
  dividir(2, 0)
rescue
  puts "Deu erro"
end
```

Bom:
Mensagem com contexto, e informativa a respeito do erro:

```
begin
  dividir(2, 0)
rescue StandardError => e
  puts "Erro ao dividir: #{e.message}"
end
```

### 3. Abstrair erros em uma classe separada

Ruim: 
Tratar diversos erros

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

Bom:
Encapsular casos em uma classe

Resultado de usar uma classe para abstrair os erros:
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
