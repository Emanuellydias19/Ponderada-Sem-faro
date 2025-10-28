
# Semáforo-Arduino-Botão

Este repositório documenta o desenvolvimento de um semáforo utilizando Arduino, incorporando um botão para travessia segura de pedestres. O objetivo principal é controlar o fluxo de veículos e pedestres em uma via, onde o semáforo permanece aberto para os veículos e só fecha quando o botão do pedestre é pressionado, garantindo uma travessia segura.

## Etapas Realizadas

1. Montagem do circuito com LEDs para semáforo: vermelho, amarelo e verde;
2. Implementação da lógica básica do semáforo para fluxo contínuo de veículos;
3. Adição do botão de travessia com resistência de 10 kΩ para estabilização do sinal;
4. Programação da nova lógica que detecta o acionamento do botão, fecha o semáforo para veículos e permite a travessia dos pedestres;
5. Testes e simulações para validação do funcionamento.

## Montagem do Circuito 

#### Componentes:

- Arduino Uno.
- Protoboard.
- LED vermelho, amarelo e verde.
- Botão pulsador.
- Resistência de 10 kΩ (para o botão).
- Fios jumper para conexões.

#### Conexões:

- LEDs conectados aos pinos digitais do Arduino, com resistores adequados para proteção dos LEDs.
- Botão conectado a um pino digital do Arduino, com resistência de 10 kΩ para evitar falsos contatos.
- GND e alimentação conectados conforme o padrão Arduino.

OBS: A montagem pode ser realizada no Tinkercad ou em ambiente físico, seguindo os diagramas elétricos fornecidos.

## Evidências

#### Capturas de Tela e Vídeos:

- **Circuito montado no Tinkercad:** (inserir link ou imagens do circuito)
- **Vídeo de Demonstração do funcionamento:** (inserir link para vídeo)
- **Sequência das luzes e acionamento do botão.**

### Referências

1. Documentação Arduino: [https://docs.arduino.cc/language-reference/pt/](https://docs.arduino.cc/language-reference/pt/)
2. Tutoriais sobre semáforos com Arduino: [https://www.youtube.com/watch?v=example](https://www.youtube.com/watch?v=example)
3. Componentes eletrônicos básicos: [https://www.electronics-tutorials.ws/](https://www.electronics-tutorials.ws/)
