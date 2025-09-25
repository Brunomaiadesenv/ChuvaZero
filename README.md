# ChuvaZero
Projeto: Varal Retrátil Automatizado com Sensor de Chuva
Curso Técnico em Desenvolvimento de Sistemas
Disciplina: IoT

Aluno: Bruno, Nicolas, Vinicius e Otávio
Professor: Fred Aguiar
1. Introdução
Este projeto consiste no desenvolvimento de um sistema automatizado para recolhimento de roupas em um varal retrátil, utilizando Arduino, um sensor de chuva e um motor controlado eletronicamente. O objetivo é proteger as roupas estendidas em caso de chuva, além de permitir ao usuário acionar manualmente o motor via aplicação em C# no computador.
2. Objetivos
- Criar um protótipo funcional de varal automatizado.
- Integrar sensor de chuva ao Arduino para acionamento automático.
- Permitir controle manual do sistema através de um aplicativo em C#.
- Demonstrar a aplicação prática de conceitos de IoT e automação residencial.
3. Materiais Utilizados
• 01 Arduino UNO (ou compatível)
• 01 Protoboard
• 01 Sensor de chuva (FC-37 ou YL-83)
• 01 Módulo Relé 5V
• 01 Motor DC 5–12V (ou Servo Motor)
• 01 Fonte de alimentação externa 9V–12V
• 02 LEDs (vermelho e verde)
• 02 Resistores 220Ω
• Cabos jumper macho-macho e macho-fêmea
• Arduino IDE
• Visual Studio ou Rider (C#)
4. Esquema de Ligações
1. Sensor de chuva → VCC (5V), GND, DO → pino D2
2. LED verde → pino D8 com resistor 220Ω
3. LED vermelho → pino D9 com resistor 220Ω
4. Módulo Relé IN → pino D7
5. Motor alimentado por fonte externa via relé
5. Funcionamento do Sistema
Modo automático: Quando o sensor detecta chuva, o Arduino aciona o relé, o motor recolhe o varal e o LED vermelho acende.
Modo manual: O usuário pode controlar o motor através da aplicação em C#, enviando comandos pela porta serial.
6. Código Arduino

/*
  ==   VARAL INTELIGENTE v1.1 (Baseado em Tempo com Monitoramento)  ==
  ATENÇÃO: Versão simplificada SEM sensores de fim de curso.
  O motor funciona por um tempo fixo. É crucial calibrar a variável TEMPO_MOVIMENTO.
*/

// --- PINOS ---
// Pinos de controle do Motor L28N
#define PINO_IN1 5
#define PINO_IN2 6

// Pino do Sensor de Chuva (Analógico)
#define PINO_ANALOGICO_CHUVA A5

// --- PARÂMETROS DE CONTROLE ---
// Limite analógico para considerar chuva (valores menores = chuva)
#define LIMITE_CHUVA 600

// TEMPO (em milissegundos) que o motor ficará ligado para mover o varal.
// !! VOCÊ PRECISA CALIBRAR ESTE VALOR !!
#define TEMPO_MOVIMENTO 1300 // Valor inicial de exemplo: 5 segundos

// --- VARIÁVEIS DE ESTADO ---
// 'true' se o varal estiver recolhido, 'false' se estiver estendido.
bool varalEstaRecolhido = false;

// --- FUNÇÃO SETUP ---
void setup() {
  Serial.begin(9600);
  Serial.println("Inicializando Varal Inteligente (versao baseada em tempo)...");

  // Configura os pinos do motor como saída
  pinMode(PINO_IN1, OUTPUT);
  pinMode(PINO_IN2, OUTPUT);

  // Garante que o motor comece parado
  digitalWrite(PINO_IN1, LOW);
  digitalWrite(PINO_IN2, LOW);

  Serial.println("Sistema pronto. Assumindo que o varal comeca estendido.");
}

// --- FUNÇÃO LOOP ---
void loop() {
  // Lê o sensor e imprime no monitor serial
  int valorChuva = analogRead(PINO_ANALOGICO_CHUVA);
  Serial.print("Leitura do Sensor de Chuva: ");
  Serial.print(valorChuva);
  
  bool estaChovendo = (valorChuva < LIMITE_CHUVA);

  // Se está chovendo E o varal não está recolhido...
  if (estaChovendo && !varalEstaRecolhido) {
    Serial.println(" -> Chuva detectada! Recolhendo o varal...");

    // Aciona o motor para recolher
    digitalWrite(PINO_IN1, LOW);
    digitalWrite(PINO_IN2, HIGH);
    delay(TEMPO_MOVIMENTO); // Espera o tempo definido
    digitalWrite(PINO_IN2, LOW); // Para o motor

    varalEstaRecolhido = true; // Atualiza o estado
    Serial.println("Movimento de recolhimento finalizado.");
  }
  // Se NÃO está chovendo E o varal está recolhido...
  else if (!estaChovendo && varalEstaRecolhido) {
    Serial.println(" -> Tempo bom! Estendendo o varal...");

    // Aciona o motor para estender
    digitalWrite(PINO_IN1, HIGH);
    digitalWrite(PINO_IN2, LOW);
    delay(TEMPO_MOVIMENTO); // Espera o tempo definido
    digitalWrite(PINO_IN1, LOW); // Para o motor

    varalEstaRecolhido = false; // Atualiza o estado
    Serial.println("Movimento de extensao finalizado.");
  } else {
      // Imprime apenas o status se nada acontecer
      if (varalEstaRecolhido) {
          Serial.println(" -> Status: Varal recolhido, aguardando tempo bom.");
      } else {
          Serial.println(" -> Status: Varal estendido, monitorando chuva.");
      }
  }

  // Espera um pouco antes de verificar novamente
  delay(1000);
}

7. Código C#

using System;
using System.IO.Ports;

class Program {
    static void Main() {
        SerialPort porta = new SerialPort("COM3", 9600);
        porta.Open();

        Console.WriteLine("Controle do Varal Retrátil");
        Console.WriteLine("M - Recolher varal");
        Console.WriteLine("L - Estender varal");
        Console.WriteLine("A - Voltar para automático");

        while (true) {
            var tecla = Console.ReadKey(true).KeyChar;
            porta.Write(tecla.ToString());
        }
    }
}

8. Resultados Esperados
- Varal se recolhe automaticamente quando chove.
- Controle manual via C# funcionando corretamente.
- LEDs indicam estados do sistema.
- Integração hardware + software demonstrada com sucesso.
9. Conclusão
O projeto demonstrou como sensores, atuadores e software podem ser integrados para criar soluções inovadoras em automação residencial. O varal retrátil inteligente é uma solução prática que exemplifica o uso de IoT e integração entre Arduino e C#.

