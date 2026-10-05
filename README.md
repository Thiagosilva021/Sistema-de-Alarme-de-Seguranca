# 🚨 Sistema de Alarme com Sensor de Movimento (Arduino UNO)

Projeto acadêmico de um sistema de alarme simples baseado em Arduino. Quando o sensor PIR detecta movimento, o sistema dispara um alarme sonoro e visual (buzzer + dois LEDs alternados) durante 30 segundos.

## 🎥 Demonstração

Vídeo do projeto físico em funcionamento:

▶️ **[Assista no YouTube](https://www.youtube.com/shorts/Ux_v_SvUQYM)**

> 🔧 **Projeto físico:** a montagem real foi feita utilizando uma placa **Arduino Galileu**. O esquema da simulação (Arduino UNO) segue a mesma lógica de ligação e o mesmo código.

---

## 📋 Sumário

- [Demonstração](#-demonstração)
- [Objetivo](#-objetivo)
- [Componentes](#-componentes)
- [Esquema de ligação](#-esquema-de-ligação)
- [Funcionamento](#-funcionamento)
- [Código-fonte](#-código-fonte)
- [Como executar](#-como-executar)
- [Parâmetros ajustáveis](#-parâmetros-ajustáveis)
- [Limitações e melhorias futuras](#-limitações-e-melhorias-futuras)

---

## 🎯 Objetivo

Desenvolver um protótipo de alarme de presença utilizando a plataforma Arduino, aplicando conceitos de:

- leitura de sensores (entrada analógica);
- acionamento de atuadores (LEDs e buzzer);
- controle de tempo com `millis()` e `delay()`;
- comunicação serial para monitoramento e depuração.

## 🧰 Componentes

| Qtd. | Componente                         |
|------|------------------------------------|
| 1    | Arduino Galileu (projeto físico) / Arduino UNO (simulação) |
| 1    | Sensor de movimento PIR (Parallax) |
| 1    | Buzzer piezoelétrico               |
| 1    | LED azul                           |
| 1    | LED vermelho                       |
| 3    | Resistores (limitação de corrente) |
| 1    | Protoboard                         |
| —    | Jumpers                            |
| 1    | Cabo USB (alimentação e upload)    |

## 🔌 Esquema de ligação

| Componente         | Pino do Arduino | Observação                          |
|--------------------|-----------------|-------------------------------------|
| Sensor PIR (saída) | `A0`            | Sinal do sensor                     |
| Sensor PIR (VCC)   | `5V`            | Alimentação                         |
| Sensor PIR (GND)   | `GND`           | Terra comum                         |
| LED de alerta 1    | `D5`            | Em série com resistor               |
| LED de alerta 2    | `D6`            | Em série com resistor               |
| Buzzer (+)         | `D7`            | Buzzer (−) ligado ao `GND`          |

> 📷 Adicione aqui a imagem do circuito montado (ex.: `circuito.png`):
>
> `![Esquema do circuito](circuito.png)`

## ⚙️ Funcionamento

1. Ao iniciar, o sistema configura os pinos e envia a mensagem *"Sistema de alarme iniciado."* pela Serial.
2. A cada ciclo, o valor do sensor em `A0` é lido e exibido no Monitor Serial.
3. Se o valor for **maior que o limiar** (`limiarSensor = 100`), o alarme é acionado:
   - os LEDs alternam entre si a cada 500 ms;
   - o buzzer emite um tom de 1000 Hz sincronizado com o LED 1;
   - o alarme dura **30 segundos**.
4. Após os 30 segundos, todos os dispositivos são desligados e o sistema volta a monitorar o sensor.

```
        ┌──────────────┐
        │ Lê sensor A0 │◄──────────────┐
        └──────┬───────┘               │
               ▼                       │
      valor > limiar (100)?            │
        │ sim          │ não           │
        ▼              ▼               │
  Alarme por 30 s   Tudo desligado     │
  (LEDs + buzzer)        │             │
        │                │             │
        └────────────────┴─► delay 200 ms
```

## 💻 Código-fonte

O código completo está no arquivo [`Codigo.c`](Codigo.c) (pode ser aberto na Arduino IDE como `.ino`).

Principais trechos:

```cpp
const int sensorPin   = A0;  // Sensor PIR
const int ledAlerta1  = 5;   // LED de alerta 1
const int ledAlerta2  = 6;   // LED de alerta 2
const int buzzerPin   = 7;   // Buzzer

const int limiarSensor = 100;                  // Limiar de ativação
const unsigned long duracaoAlarme = 30000;     // 30 segundos
```

## ▶️ Como executar

### Opção 1 — Hardware real (Arduino Galileu)

1. Monte o circuito conforme o [esquema de ligação](#-esquema-de-ligação).
2. Abra o código na [Arduino IDE](https://www.arduino.cc/en/software) (renomeie `Codigo.c` para `Codigo.ino`, dentro de uma pasta de mesmo nome).
3. Selecione a placa correspondente ao **Arduino Galileu** e a porta correta.
4. Clique em **Upload**.
5. Abra o **Monitor Serial** (9600 baud) para acompanhar as leituras.

### Opção 2 — Simulação

O circuito pode ser simulado no [Tinkercad Circuits](https://www.tinkercad.com/circuits): monte os componentes conforme a tabela, cole o código no editor e inicie a simulação movimentando o sensor PIR.

## 🔧 Parâmetros ajustáveis

| Parâmetro        | Valor padrão | Descrição                                   |
|------------------|--------------|---------------------------------------------|
| `limiarSensor`   | `100`        | Valor de leitura que dispara o alarme       |
| `duracaoAlarme`  | `30000` ms   | Tempo total do alarme                       |
| `tone(..., 1000)`| `1000` Hz    | Frequência do som do buzzer                 |
| `delay(500)`     | `500` ms     | Intervalo de alternância entre os LEDs      |

## ⚠️ Limitações e melhorias futuras

- **Bloqueio durante o alarme:** o `while` com `delay()` impede que o Arduino leia o sensor ou responda a outros eventos durante os 30 segundos. Uma melhoria seria usar `millis()` de forma não bloqueante.
- **Leitura do PIR:** o sensor PIR possui saída digital; ele poderia ser lido com `digitalRead()` em um pino digital, o que simplificaria a lógica (o `analogRead` funciona, mas retorna apenas valores próximos de 0 ou 1023).
- **Desligamento manual:** adicionar um botão para desativar o alarme antes do fim do tempo.
- **Notificações:** integrar um módulo Wi-Fi/GSM (ex.: ESP8266) para enviar alertas ao celular.
- **Tempo de calibração do PIR:** aguardar alguns segundos após ligar o sensor antes de iniciar o monitoramento, evitando falsos disparos.

## 📄 Licença

Projeto desenvolvido para fins educacionais. Sinta-se livre para estudar, modificar e reutilizar.
