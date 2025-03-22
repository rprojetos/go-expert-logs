# LOGS


### Visão geral

O pacote log implementa um pacote de registro simples. Ele define um tipo, Logger , com métodos para formatar a saída. Ele também tem um Logger 'padrão' predefinido acessível por meio de funções auxiliares Print[f|ln], Fatal[f|ln] e Panic[f|ln], que são mais fáceis de usar do que criar um Logger manualmente. Esse logger grava no erro padrão e imprime a data e a hora de cada mensagem registrada. Cada mensagem de log é gerada em uma linha separada: se a mensagem que está sendo impressa não terminar em uma nova linha, o logger adicionará uma. As funções Fatal chamam os.Exit (1) após escrever a mensagem de log. As funções Panic chamam panic após escrever a mensagem de log.
