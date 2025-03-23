# LOGS


## Visão geral

O pacote log implementa um pacote de registro simples. Ele define um tipo, Logger , com métodos para formatar a saída. Ele também tem um Logger 'padrão' predefinido acessível por meio de funções auxiliares Print[f|ln], Fatal[f|ln] e Panic[f|ln], que são mais fáceis de usar do que criar um Logger manualmente. Esse logger grava no erro padrão e imprime a data e a hora de cada mensagem registrada. Cada mensagem de log é gerada em uma linha separada: se a mensagem que está sendo impressa não terminar em uma nova linha, o logger adicionará uma. As funções Fatal chamam os.Exit (1) após escrever a mensagem de log. As funções Panic chamam panic após escrever a mensagem de log.
ref.: [https://pkg.go.dev/log](https://pkg.go.dev/log)

## Como usar

Em Go, o pacote log oferece várias funções para registro de mensagens, e a escolha entre log.Println, log.Printf, ou outras depende do contexto e da necessidade de formatação. Vamos comparar e entender quando usar cada uma:

### 1. log.Println

Uso: Quando você quer imprimir valores separados por espaços, sem formatação complexa.

Comportamento: Adiciona espaços entre os argumentos e uma nova linha no final.

Exemplo:

```go
log.Println("Erro ao processar dados:", err, "status:", 500)
```
*`Saída: 2023/10/01 12:00:00 Erro ao processar dados: arquivo não encontrado status: 500`*

#### Boa prática:
- Use log.Println para simplicidade:
```go
log.Println("Servidor iniciado na porta", port)
```

### 2. log.Printf

Uso: Quando você precisa de formatação personalizada (ex: valores em posições específicas).

Comportamento: Funciona como fmt.Printf, mas adiciona data/hora e nova linha.

Exemplo:

```go
log.Printf("Erro ao processar %s: %v (código: %d)", "arquivo.txt", err, 500)
```
*`Saída: 2023/10/01 12:00:00 Erro ao processar arquivo.txt: permissão negada (código: 500)`*

#### Boa prática:
- Use log.Printf para mensagens detalhadas:
```go
log.Printf("Requisição falhou: método=%s, código=%d", "GET", 500)
```

### 3. log.Fatal / log.Fatalf

Uso: Quando um erro irrecuperável ocorre, e você quer encerrar o programa imediatamente.

Comportamento: Chama os.Exit(1) após logar a mensagem.

Exemplo:

```go
if err != nil {
    log.Fatalf("Erro crítico: %v", err)
}
```

*`Saída: Erro crítico: ...`*

#### Boas práticas:
- Evite log.Fatal fora do main.
    - Funções de biblioteca não devem decidir encerrar o programa.
- Prefira retornar erros em vez de usar log.Fatal em funções internas:

Exemplo:

```go
func LoadConfig() (*Config, error) {
    // ...
    if err != nil {
        return nil, fmt.Errorf("falha ao carregar config: %w", err)
    }
}
```
- Centralize o tratamento de erros:

Exemplo:

```go
func main() {
    if err := run(); err != nil {
        log.Fatalf("Erro: %v", err) // Decisão de encerrar aqui
    }
}
```

#### 4. log.Panic / log.Panicf

Uso: Quando você quer gerar um panic (geralmente evitado em produção).

Comportamento: Lança um panic após logar a mensagem (pode ser recuperado com recover).

Exemplo:

```go
if err != nil {
    log.Panicf("Erro inesperado: %v", err) // Panic + stack trace
}
```

*`Saída: Erro inesperado: ...`*


## Conclusão para log
Utilize as boas práticas para construir um código limpo com mensagens de log úteis para depuração! 😊

- Use `log.Println` para mensagens simples.

- Use `log.Printf` para mensagens formatadas.

- Reserve `log.Fatal`/`log.Fatalf` para erros críticos no main ou pontos de entrada.

- Evite `log.Panic` em código de produção.

## Conclusão no tratamento de erros

- Prefira retornar error:
Funções comuns devem retornar erros para que o caller decida como tratá-los.
Formatação de uma mensagem de erro, para rastreabilidade:
    - fmt.Errorf("erro ao processar configuração: %w", err) 
    - as mensagens de erro a serem retornados deve ser lower case.

- Use log.Fatal() apenas no main:
    - Decisões de encerrar o programa devem ficar restritas ao ponto de entrada.

- Evite panic() em bibliotecas:
    - Bibliotecas não devem forçar a saída do programa. Sempre retorne error.

- Use recover() apenas em casos específicos:
    - Exemplo: Em servidores HTTP, para evitar que um panico derrube todo o serviço.

- log.Fatal(): Para erros críticos na inicialização (só no main).

- log.Panic(): Quase nunca (use retorno de error).

- panic(): Para bugs ou estados inválidos nunca esperados.
