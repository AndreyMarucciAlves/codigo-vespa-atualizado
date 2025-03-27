# codigo-vespa-atualizado


#include <RoboCore_Vespa.h>

VespaMotors motores; // determinou "motores" como um objeto pertencente a classe VespaMotors.

const int PINO_TRIGGER = 25; // esse pino serve para emitir raio infravermelho no ambiente. (output)
const int PINO_ECHO = 26; // esse pino serve para receber dados do meio ambiente. (input)
const int DISTANCIA_OBSTACULO = 25; // é a distancia minima estabelecida de um obstaculo. 
const int VELOCIDADE = 100; // este determina a velocidade do robo.
const int ESPERA = 60; // este determina o tempo de resposta do robo.
const int ESPERA_MOVIMENTO = 250; // este determina o tempo para analise de movimentos(determinar o movimento) do robo.

int distancia;

void setup() {
  pinMode(PINO_TRIGGER, OUTPUT); // determina o PINO_TRIGGER como output.
  pinMode(PINO_ECHO, INPUT); // determina o PINO_ECHO como input.
  digitalWrite(PINO_TRIGGER, LOW); // o DigitalWrite "compila" os dados recebidos dos pinos e passa pra central. Como esta opção esta "LOW" (desligado), não realizara nenhuma ação.

  // Inicializa a comunicação serial para imprimir os dados
  Serial.begin(9600);
}

void loop() {
  delay(ESPERA);
  distancia = sensor_ultrassonico(PINO_TRIGGER, PINO_ECHO); // esse compara a distancia do robo no ambiente com a distancia minima determinada no sistema.

    // Imprime a distância do obstáculo
  Serial.print("Distancia do obstáculo: ");
  Serial.print(distancia);
  Serial.println(" cm");


  if (distancia <= DISTANCIA_OBSTACULO) { // caso a distania real seja menor ou igual a determinada, acionara denovo o sensor.
    delay(ESPERA); 
    distancia = sensor_ultrassonico(PINO_TRIGGER, PINO_ECHO);
    

    if (distancia <= DISTANCIA_OBSTACULO) { // nesse if eo robo voltara pra tras.
      Serial.println("obstaculo detectado, voltando para tras.");
      motores.stop();
      motores.backward(VELOCIDADE);
      delay(ESPERA_MOVIMENTO);
      motores.stop();

      if (millis() % 2 == 0) { //nesse if ele determinará para virar à direita.
        Serial.println("virando à direita!");
        motores.turn(VELOCIDADE, -VELOCIDADE);
        delay(ESPERA_MOVIMENTO);
        motores.stop();
        distancia = sensor_ultrassonico(PINO_TRIGGER, PINO_ECHO);
        

        while (distancia <= DISTANCIA_OBSTACULO) { // enquanto haver um obstáculo na frente do sensor ele continuará girando.
          Serial.println("Ainda há um obstáculo.");
          motores.turn(VELOCIDADE, -VELOCIDADE);
          delay(ESPERA);
          distancia = sensor_ultrassonico(PINO_TRIGGER, PINO_ECHO);
          
        }
      } else {  //nesse if ele determinará para virar à esquerda.
        Serial.println("virando à esquerda!");
        motores.turn(-VELOCIDADE, VELOCIDADE);
        delay(ESPERA_MOVIMENTO);
        motores.stop();
        distancia = sensor_ultrassonico(PINO_TRIGGER, PINO_ECHO);
        

        while (distancia <= DISTANCIA_OBSTACULO) { // enquanto haver um obstáculo na frente do sensor ele continuará girando.
          Serial.println("Ainda há um obstaculo.");
          motores.turn(-VELOCIDADE, VELOCIDADE);
          delay(ESPERA);
          distancia = sensor_ultrassonico(PINO_TRIGGER, PINO_ECHO);
          
        }
      }
    }
  } else { //após verificar que não há um obstáculo a frente, ele se movimenta pra frente novamente.
    motores.forward(VELOCIDADE);
  }
}

int sensor_ultrassonico(int pinotrigger, int pinoecho) {
  digitalWrite(pinotrigger, HIGH); // aqui ele compilará os dados recebidos dos sensores pis esta "HIGH"(ligado).
  delayMicroseconds(10);
  digitalWrite(pinotrigger, LOW);
  return pulseIn(pinoecho, HIGH); // 58;
}
