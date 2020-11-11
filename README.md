# Trabalho CAP. 6 e 7 Clean Code - UNISEP
Trabalho sobre tratamento de erros e limites, seguindo o livro Clean Code

Utilizando a linguagem Ruby

## Tratamento de Erros

Um jeito de escrever código limpo e robusto, que trata erros com elegancia e estilo.

### 1. Usar begin rescue no lugar de if e else

Ruim: 

Usando if e else para tratar erros
```
x = nil
if (x != nil)
  x.length
else
  puts "erro!!!"
end
```

Bom:
Utilizar o begin rescue para tratar o possível erro

```
x = nil
begin
  x.length
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
