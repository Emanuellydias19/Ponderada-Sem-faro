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


O código possui comentários assim como sua explicação para melhor entendimento e aprofundamento da lógica e conceitos. Essa prática ajuda a tornar o programa mais compreensível, facilitando futuras alterações, manutenção e o aprendizado de quem estiver lendo. Comentários bem elaborados promovem uma documentação interna eficiente, explicando o propósito de cada trecho e o funcionamento geral do sistema, conforme boas práticas recomendadas na programação de Arduino. <br>

``` 
// 1. Definição dos Pinos e Variáveis

const int PIN_VERMELHO = 10;
const int PIN_AMARELO = 9;
const int PIN_VERDE = 8;
const int PIN_BOTAO = 12;

// Variáveis de Controle de Tempo do Semáforo

unsigned long tempoAnteriorSemaforo = 0;

// O tempo inicial será de 6000ms (Verde)

long intervaloSemaforo = 6000; 

// Variáveis da Máquina de Estados do Semáforo

const int leds[] = {PIN_VERMELHO, PIN_AMARELO, PIN_VERDE};

// Ordem: {VERDE, AMARELO, VERMELHO} 
const long tempos[] = {4000, 2000, 6000}; 

int estadoAtual = 2; 
bool cicloAtivo = false;


// 2. Variáveis de Debounce do Botão

const long INTERVALO_DEBOUNCE = 50; 
int estadoFinalBotao = HIGH;      
unsigned long tempoUltimaMudanca = 0;
int ultimoEstadoBotao = HIGH;



// 3. Função de Lógica do Botão (Máquina de Estados de Debounce)

void lerBotao() {
  int leitura = digitalRead(PIN_BOTAO);

  if (leitura != ultimoEstadoBotao) {
    tempoUltimaMudanca = millis();
  }

  if ((millis() - tempoUltimaMudanca) > INTERVALO_DEBOUNCE) {
    if (leitura != estadoFinalBotao) {
      estadoFinalBotao = leitura;

      // Executa a ação APENAS QUANDO O BOTÃO É PRESSIONADO (Transição de HIGH para LOW)
      if (estadoFinalBotao == LOW) {
        acionarSemaforo();
      }
    }
  }

  ultimoEstadoBotao = leitura;
}


// 4. Função que Inicia a Transição do Semáforo

void acionarSemaforo() {
  // Só aciona se o semáforo estiver no estado inicial (VERDE) e inativo.

  if (!cicloAtivo && estadoAtual == 2) {
    
    // Inicia o ciclo, pulando o Verde para o Amarelo (estado 1)

    estadoAtual = 1; 
    cicloAtivo = true;
    
    // Configura o tempo e a cor do novo estado (Amarelo = 2000ms)

    tempoAnteriorSemaforo = millis();
    intervaloSemaforo = tempos[estadoAtual];
    
    // Liga o Amarelo

    digitalWrite(PIN_VERDE, LOW);
    digitalWrite(PIN_AMARELO, HIGH);
    digitalWrite(PIN_VERMELHO, LOW);
  }
}

// 5. Função da Máquina de Estados do Semáforo

void avancarSemaforo() {
  if (cicloAtivo) {
    if (millis() - tempoAnteriorSemaforo >= intervaloSemaforo) {
      
      tempoAnteriorSemaforo = millis();

      // Logica: AMARELO(1) -> VERMELHO(0) -> VERDE(2) -> [Fim do Ciclo]
      if (estadoAtual == 1) {       
        estadoAtual = 0;             
      } else if (estadoAtual == 0) { 
        estadoAtual = 2;             
        cicloAtivo = false;          
      }
      
      // Define o tempo do novo estado com o valor do array `tempos`
      intervaloSemaforo = tempos[estadoAtual];

      // Atualiza os LEDs com base no novo estado
      digitalWrite(PIN_VERMELHO, (estadoAtual == 0) ? HIGH : LOW);
      digitalWrite(PIN_AMARELO, (estadoAtual == 1) ? HIGH : LOW);
      digitalWrite(PIN_VERDE, (estadoAtual == 2) ? HIGH : LOW);
    }
  }
}

void setup() {
  pinMode(PIN_VERMELHO, OUTPUT);
  pinMode(PIN_AMARELO, OUTPUT);
  pinMode(PIN_VERDE, OUTPUT);
  pinMode(PIN_BOTAO, INPUT_PULLUP);

  // Estado Inicial: Verde aceso (6s)
  digitalWrite(PIN_VERDE, HIGH);
  digitalWrite(PIN_VERMELHO, LOW);
  digitalWrite(PIN_AMARELO, LOW);
}

void loop() {
  lerBotao();
  avancarSemaforo();
}
```


### 🚦 Explicação Didática: Semáforo Não-Bloqueante

Este código implementa um semáforo de trânsito que alterna automaticamente entre as cores Verde, Amarelo e Vermelho, sem nunca "travar" o Arduino. Ele usa o conceito de Máquina de Estados e o tempo do sistema (`millis()`) para ser Não-Bloqueante.

---

## 1. Definições Iniciais (Setup)

### Variáveis Essenciais

- **Pinos:** Definem a conexão de cada LED no seu Arduino (Ex: `PIN_VERMELHO = 10`).
- **Estados:** Definem os identificadores numéricos para as cores: `ESTADO_VERMELHO (0)`, `ESTADO_AMARELO (1)`, `ESTADO_VERDE (2)`.
- **Tempos (`tempos[]`):** Um array que guarda a duração de cada estado (em milissegundos). Por exemplo: `{4000, 2000, 6000}`.
- **Sequência (`sequenciaEstados[]`):** O "roteiro" do semáforo: Verde → Amarelo → Vermelho.
- **Controle de Tempo:**  
  - `tempoAnteriorSemaforo`: Armazena o momento exato em que o estado atual começou.  
  - `intervaloSemaforo`: Armazena por quanto tempo o estado atual deve durar (ex: 6000ms para o Verde).

### Função setup()

A função `setup()` é executada apenas uma vez, no início. Ela faz o seguinte:  
- Configura os pinos: Define os pinos dos LEDs como OUTPUT (saída).  
- Estado Inicial: Acende o LED Verde (`digitalWrite(PIN_VERDE, HIGH)`) e apaga os outros.  
- Inicia o Cronômetro: Define `tempoAnteriorSemaforo = millis();`. Isso "zera" o cronômetro para que o primeiro estado (Verde) comece a contar a partir de agora.

---

## 2. O Segredo do Não-Bloqueante (`avancarSemaforo()`)

A função `avancarSemaforo()` é o coração do código e garante que o Arduino nunca pare.

### Como Funciona a Transição

O código usa um princípio chamado "Controle de Tempo Baseado em Diferença":

if (millis() - tempoAnteriorSemaforo >= intervaloSemaforo) {
// ... é hora de mudar de estado!
}

text

- `millis()`: Diz a você há quantos milissegundos o Arduino está ligado.  
- `tempoAnteriorSemaforo`: Diz a você há quantos milissegundos o estado começou.  
- Subtração: A diferença (`millis() - tempoAnteriorSemaforo`) é quanto tempo se passou desde o início do estado.  
- Comparação: Se o tempo que se passou for maior ou igual ao tempo que o estado deveria durar (`intervaloSemaforo`), a condição é verdadeira e o semáforo avança!

🔑 **Por que é Não-Bloqueante?**  
Porque ele não espera! O código simplesmente verifica a condição (`if`) e segue em frente. Se for verdadeiro, ele muda; se for falso, ele ignora o bloco de código e passa para a próxima instrução no `loop()`.

### Lógica de Mudança de Estado

Quando é hora de mudar, três coisas acontecem:  
- Atualiza o Cronômetro: `tempoAnteriorSemaforo = millis();` – Isso "reinicia" a contagem de tempo para o próximo estado que está entrando.  
- Avança o Estado:
indiceSequenciaAtual = (indiceSequenciaAtual + 1) % TOTAL_ESTADOS;

text
Avança o ponteiro para o próximo item no roteiro (Verde → Amarelo → Vermelho → Verde...). O operador de módulo (%) garante que, ao chegar no final, ele volte para o início.  
- Atualiza os LEDs: O código usa o Operador Ternário para de forma compacta acender a luz do estado atual e apagar as outras:
digitalWrite(PIN_VERMELHO, (estadoAtual == ESTADO_VERMELHO) ? HIGH : LOW);

text
(Se for o estado Vermelho, acende; senão, apaga).

---

## 3. O Loop Infinito (`loop()`)

A função `loop()` é a alma de todo código Arduino. No seu programa, ela é muito simples:

void loop() {
avancarSemaforo();
}

text

A única coisa que o Arduino faz, trilhões de vezes por segundo, é chamar a função `avancarSemaforo()`.

---

## Conclusão

O Arduino não está "parado, esperando" por 6 segundos. Ele está executando o `loop()` rapidamente, e a cada execução, ele pergunta: "O tempo expirou?" Ele só entra no bloco de mudança de estado quando a resposta é "Sim". Isso garante que ele esteja sempre pronto para executar outras tarefas, se você as adicionasse ao `loop()`.

````

// 1. Definição dos Pinos e Variáveis
const int PIN_VERMELHO = 10;
const int PIN_AMARELO = 9;
const int PIN_VERDE = 8;
const int PIN_BOTAO = 12;

// Variáveis de Controle de Tempo do Semáforo
unsigned long tempoAnteriorSemaforo = 0;
long intervaloSemaforo = 5000; // Tempo inicial (Verde)

// Variáveis da Máquina de Estados do Semáforo
const int leds[] = {PIN_VERMELHO, PIN_AMARELO, PIN_VERDE};
const long tempos[] = {5000, 2000, 5000}; // [Vermelho, Amarelo, Verde]
int estadoAtual = 2; // Começa no estado 2 (VERDE)
bool cicloAtivo = false;

// ----------------------------------------------------
// 2. Variáveis de Debounce do Botão (Totalmente Não-Bloqueante)
const long INTERVALO_DEBOUNCE = 50; // 50ms é o tempo padrão para debounce
int estadoLeituraBotao = HIGH;      // O estado atual do pino do botão (HIGH = PULLUP/Não Pressionado)
int estadoFinalBotao = HIGH;        // O estado estável do botão
unsigned long tempoUltimaMudanca = 0;

// Variável para armazenar o estado anterior do botão (para detectar mudança)
int ultimoEstadoBotao = HIGH;


// 3. Função de Lógica do Botão (Máquina de Estados de Debounce)
void lerBotao() {
  // LÊ O ESTADO ATUAL DO BOTÃO
  int leitura = digitalRead(PIN_BOTAO);

  // DETECTA MUDANÇA (TRANSITÓRIA)
  if (leitura != ultimoEstadoBotao) {
    // A leitura mudou, reinicia o cronômetro de tempo
    tempoUltimaMudanca = millis();
  }

  // CONFIRMA ESTADO ESTÁVEL APÓS O INTERVALO
  if ((millis() - tempoUltimaMudanca) > INTERVALO_DEBOUNCE) {
    // Se a leitura for diferente do estadoFinal (estável)...
    if (leitura != estadoFinalBotao) {
      // O estado final mudou (o botão foi REALMENTE pressionado ou solto)
      estadoFinalBotao = leitura;

      // Executa a ação APENAS QUANDO O BOTÃO É PRESSIONADO (Transição de HIGH para LOW)
      if (estadoFinalBotao == LOW) {
        acionarSemaforo();
      }
    }
  }

  // SALVA O ESTADO ATUAL PARA A PRÓXIMA COMPARAÇÃO
  ultimoEstadoBotao = leitura;
}


// 4. Função que Inicia a Transição do Semáforo
void acionarSemaforo() {
  // Só aciona se o semáforo estiver no estado inicial (VERDE) e inativo.
  if (!cicloAtivo && estadoAtual == 2) {
    
    // Inicia o ciclo, pulando o Verde para o Amarelo
    estadoAtual = 1; 
    cicloAtivo = true;
    
    // Configura o tempo e a cor do novo estado (Amarelo)
    tempoAnteriorSemaforo = millis();
    intervaloSemaforo = tempos[estadoAtual];
    
    digitalWrite(PIN_VERDE, LOW);
    digitalWrite(PIN_AMARELO, HIGH);
    digitalWrite(PIN_VERMELHO, LOW);
  }
}

// 5. Função da Máquina de Estados do Semáforo
void avancarSemaforo() {
  if (cicloAtivo) {
    if (millis() - tempoAnteriorSemaforo >= intervaloSemaforo) {
      
      tempoAnteriorSemaforo = millis();

      // Logica: AMARELO(1) -> VERMELHO(0) -> VERDE(2) -> [Fim do Ciclo]
      if (estadoAtual == 1) {        // Se estava AMARELO...
        estadoAtual = 0;             // ... vai para VERMELHO
      } else if (estadoAtual == 0) { // Se estava VERMELHO...
        estadoAtual = 2;             // ... volta para VERDE
        cicloAtivo = false;          // Desliga o ciclo (espera novo botão)
      }
      
      // O tempo do novo estado
      intervaloSemaforo = tempos[estadoAtual];

      // Atualiza os LEDs com base no novo estado
      digitalWrite(PIN_VERMELHO, (estadoAtual == 0) ? HIGH : LOW);
      digitalWrite(PIN_AMARELO, (estadoAtual == 1) ? HIGH : LOW);
      digitalWrite(PIN_VERDE, (estadoAtual == 2) ? HIGH : LOW);
    }
  }
}

void setup() {
  pinMode(PIN_VERMELHO, OUTPUT);
  pinMode(PIN_AMARELO, OUTPUT);
  pinMode(PIN_VERDE, OUTPUT);
  pinMode(PIN_BOTAO, INPUT_PULLUP);

  // Estado Inicial: Verde aceso
  digitalWrite(PIN_VERDE, HIGH);
  digitalWrite(PIN_VERMELHO, LOW);
  digitalWrite(PIN_AMARELO, LOW);
}

void loop() {
  // O loop agora só chama funções não-bloqueantes.
  // O programa nunca trava!

  lerBotao();
  avancarSemaforo();
}

```