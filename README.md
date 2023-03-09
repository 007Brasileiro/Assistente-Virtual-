# Assistente-Virtual-
Assistente Virtual Usando ESP8266

Configurar o ESP8266 para se conectar à rede Wi-Fi local.

Configurar o módulo ESP8266 para receber comandos de voz através do protocolo MQTT.

Configurar um dispositivo compatível com Alexa (como um Echo Dot) para se comunicar com o ESP8266 via MQTT.

Escrever um código que processa os comandos de voz recebidos pelo ESP8266 e executa a ação correspondente.

Aqui está um exemplo de código que pode ser usado para implementar um assistente virtual básico usando o ESP8266:



#include <ESP8266WiFi.h>
#include <PubSubClient.h>

// Definir as credenciais da rede WiFi local
const char* ssid = "NOME_DA_REDE";
const char* password = "SENHA_DA_REDE";

// Definir as credenciais do broker MQTT
const char* mqtt_server = "BROKER_MQTT";
const char* mqtt_username = "USUARIO_MQTT";
const char* mqtt_password = "SENHA_MQTT";
const char* mqtt_topic = "TOPICO_MQTT";

// Definir os pinos de entrada e saída
const int ledPin = 2; // LED para indicar que o comando foi executado com sucesso

// Inicializar o cliente WiFi e o client MQTT
WiFiClient espClient;
PubSubClient client(espClient);

void setup() {
  // Inicializar a comunicação serial
  Serial.begin(115200);

  // Conectar ao WiFi local
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.println("Conectando ao WiFi...");
  }

  // Configurar o cliente MQTT
  client.setServer(mqtt_server, 1883);
  client.setCallback(callback);

  // Configurar os pinos de entrada e saída
  pinMode(ledPin, OUTPUT);

  // Conectar ao broker MQTT
  while (!client.connected()) {
    Serial.println("Conectando ao broker MQTT...");
    if (client.connect("ESP8266Client", mqtt_username, mqtt_password)) {
      Serial.println("Conectado ao broker MQTT!");
      client.subscribe(mqtt_topic);
    } else {
      Serial.print("Falha na conexão com o broker MQTT, rc=");
      Serial.println(client.state());
      delay(5000);
    }
  }
}

void loop() {
  // Verificar se há mensagens novas no tópico MQTT
  client.loop();
}

// Função de callback para processar as mensagens recebidas no tópico MQTT
void callback(char* topic, byte* payload, unsigned int length) {
  Serial.print("Mensagem recebida [");
  Serial.print(topic);
  Serial.print("] ");
  
  // Converter a mensagem recebida em uma string
  String message = "";
  for (int i = 0; i < length; i++) {
    message += (char)payload[i];
  }

  // Verificar qual comando foi recebido
  if (message == "acender_led") {
    digitalWrite(ledPin, HIGH);
    client.publish(mqtt_topic, "LED aceso!");
  } else if (message == "apagar_led") {
    digitalWrite(led





