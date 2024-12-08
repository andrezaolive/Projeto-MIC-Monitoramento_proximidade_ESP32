*Disciplina:* MIC014 – Hands-On Basic Desenvolvimento Orientado a Testes  
*Atividade:* Maker Aula 4   
*Nome dos participantes:* Andreza Oliveira Gonçalves, Fábio Aurélio Barros Alexandre, Jonathan Emerson Braga da Silva  
 
## **Projeto de monitoramento e contagem de higienização das mãos com ESP32**

Desenvolvemos um sistema com ESP32 utilizando um sensor PIR para detectar movimento e um sensor ultrassônico HC-SR04 para verificar a proximidade de pessoas que realizam a higienização das mãos em ambientes hospitalares. Se ambos os critérios forem atendidos (movimento detectado e distância menor ou igual a uma estipulada), o ESP32 aciona um LED, um buzzer e exibe no monitor serial a mensagem de "Pessoa detectada próxima ao dispenser. Distância detectada: " com o valor da distância. Caso contrário, exibe que nenhum objeto está próximo. Além disso, implementamos um botão para fazer a contagem de detecções realizadas.

```c
#define TRIGGER_PIN 13     
#define ECHO_PIN 12        
#define BUTTON_PIN 14       
long duration;             
int distancia;             
int contador = 0;
const int PIN_BUZZER = 5;
const int PIN_PIR = 23;
const int PIN_BUTTON = 14;
const int PIN_LED = 22;

void setup() {
  pinMode(PIN_BUZZER,OUTPUT);
  pinMode(PIN_PIR, INPUT);
  pinMode(PIN_BUTTON,INPUT_PULLUP);
  pinMode(PIN_LED, OUTPUT);
  Serial.begin(115200);
  pinMode(TRIGGER_PIN, OUTPUT);    
  pinMode(ECHO_PIN, INPUT);        
  
  Serial.print("Sensor de movimento\n");
  Serial.print("Ready!\n");
  Serial.println("Sistema de Monitoramento de Dispenser Iniciado");
}

void loop() {
  digitalWrite(TRIGGER_PIN, LOW);
  delayMicroseconds(2);
  digitalWrite(TRIGGER_PIN, HIGH);
  delayMicroseconds(10);
  digitalWrite(TRIGGER_PIN, LOW);
  digitalWrite(PIN_LED, LOW);
  duration = pulseIn(ECHO_PIN, HIGH);
            
  // Calcula a distância em centímetros
  distancia = duration * 0.034 / 2;

  if (digitalRead(PIN_PIR) == HIGH) {
            // Lê a duração do pulso no pino E
            if (distancia > 0 && distancia <=10) {  //detecta até 20 cm
                Serial.print("Pessoa detectada próxima ao dispenser. Distância detectada: ");
                Serial.print(distancia);
                Serial.println(" cm.");


                // Verifica se o botão foi pressionado para simular o uso do dispenser
                if (digitalRead(BUTTON_PIN) == LOW) { // LOW indica que o botão foi pressionado
                  delay(50);
                  if (digitalRead(BUTTON_PIN) == LOW) {
                    digitalWrite(PIN_LED, HIGH);
                    digitalWrite(PIN_BUZZER, HIGH);
                    contador++; // Incrementa o contador de usos do dispenser
                    Serial.print("Dispenser utilizado. Total de usos: ");
                    Serial.println(contador);

                    while (digitalRead(BUTTON_PIN) == LOW);
                    digitalWrite(PIN_LED, LOW);
                    digitalWrite(PIN_BUZZER, LOW);
                  }
                }
              } else {
                Serial.println("Sem detecção.");
              }  
  }else{
  digitalWrite(PIN_BUZZER,LOW);
  }
  delay(100);
}
```
## Mudanças implementadas: 
**Precisão no monitoramento com HC-SR04:** Mostra a distância no monitor serial se for menor ou igual à inicialmente definida.  
**Melhora na transmissão de informação:** Aciona um LED e um buzzer como alerta caso o botão seja acionado, realizando a contagem de acionamentos.  
**Informa ausência:** Caso o sensor não faça detecção, é informado no monitor que nenhum objeto está próximo do dispenser.
