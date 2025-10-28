# Semáforo com Arduino e Botão para Travessia de Pedestres

## Descrição do Projeto

Esta versão do semáforo com Arduino introduz uma funcionalidade essencial para segurança dos pedestres: um botão que os pedestres podem pressionar para solicitar a travessia segura. Ao contrário da versão básica, onde o semáforo funciona em um ciclo fixo, esse sistema permanece aberto para o trânsito de veículos e só ativa o sinal de fechamento para os veículos, abrindo a passagem para os pedestres, quando o botão for pressionado.

## Funcionamento

- O semáforo fica normalmente aberto para os veículos.
- Quando um pedestre pressiona o botão (com resistência de 10 kΩ para estabilizar o sinal), a lógica muda para fechar o fluxo de veículos e abrir a possibilidade de travessia segura.
- A sequência das luzes é controlada para garantir o tempo suficiente para que os pedestres atravessem com segurança.
- Após o tempo definido, o semáforo retorna ao estado normal, sempre aberto para o trânsito.

## Componentes Utilizados

- Arduino (modelo compatível)
- LEDs para as cores do semáforo (vermelho, amarelo, verde)
- Botão pulsador para acionamento do pedido de travessia
- Resistência de 10 kΩ para o botão
- Fios e protoboard para montagem do circuito

## Implementação

- Montagem dos LEDs conforme disposição tradicional do semáforo.
- Conexão do botão ao Arduino com a resistência de 10 kΩ para evitar falsos acionamentos.
- Programação da lógica que monitora o estado do botão e altera a sequência de luzes baseada na interação do pedestre.
- Garantia de tempos adequados para a segurança e fluxo eficiente de veículos e pedestres.

## Possíveis Melhorias Futuras

- Adição de feedback visual ou sonoro para confirmação do acionamento do botão.
- Integração com sensores para detectar presença de veículos e pedestres.
- Interface de configuração para ajustar os tempos de cada fase diretamente pelo usuário.