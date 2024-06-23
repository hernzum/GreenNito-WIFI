/*******************************************************************
    Un bot de Telegram que te envía un mensaje cuando el ESP se inicia

    Este sketch utiliza WiFiManager para la configuración de WiFi de forma dinámica.
    Después de cargar el sketch, busca y conéctate a la red WiFi 'AutoConnectAP'.
    Abre un navegador y completa la configuración WiFi.

    Partes:
    D1 Mini ESP8266 * - http://s.click.aliexpress.com/e/uzFUnIe
    (o cualquier placa ESP8266)

      = Afiliado

    Si encuentras útil lo que hago y te gustaría apoyarme,
    por favor considera convertirte en patrocinador en Github
    https://github.com/sponsors/witnessmenow/

    Escrito por Brian Lough
    YouTube: https://www.youtube.com/brianlough
    Tindie: https://www.tindie.com/stores/brianlough/
    Twitter: https://twitter.com/witnessmenow

    Optimizado y comentado en español por Nito
 *******************************************************************/

#include <ESP8266WiFi.h>
#include <WiFiManager.h> // Librería WiFiManager
#include <WiFiClientSecure.h>
#include <UniversalTelegramBot.h>
#include <ArduinoJson.h>

// Token del bot de Telegram (obtenido de Botfather)
#define BOT_TOKEN "7232676749:AAF_2nxG2fgiEXRYMtMc7kC3dcPA3P8KhdA"
// ID de chat de Telegram para enviar mensajes
#define CHAT_ID "18620566"

// Inicialización de certificados y cliente seguro
X509List cert(TELEGRAM_CERTIFICATE_ROOT);
WiFiClientSecure secured_client;
UniversalTelegramBot bot(BOT_TOKEN, secured_client);

const int moistPin = A0; // Pin para el sensor de humedad del suelo
int lastSoilSensor = -1; // Última lectura del sensor de humedad del suelo
const int threshold = 5; // Cambio mínimo en el valor del sensor para enviar un nuevo mensaje

unsigned long previousMillis = 0; // Almacena la última vez que se realizó una lectura del sensor
const long interval = 1000; // Intervalo para comprobar el sensor (1 segundo)

void setup() {
  Serial.begin(115200);
  Serial.println();

  // Configuración de WiFi utilizando WiFiManager
  WiFiManager wifiManager;
  wifiManager.autoConnect("AutoConnectAP");

  Serial.print("Conectado a la red WiFi. Dirección IP: ");
  Serial.println(WiFi.localIP());
  
  secured_client.setTrustAnchors(&cert); // Agregar certificado raíz para api.telegram.org

  // Obtener la hora de NTP
  Serial.print("Recuperando la hora: ");
  configTime(0, 0, "pool.ntp.org"); // obtener la hora UTC vía NTP
  time_t now = time(nullptr);
  while (now < 24 * 3600) {
    Serial.print(".");
    delay(100);
    now = time(nullptr);
  }
  Serial.println(now);

  pinMode(LED_BUILTIN, OUTPUT); // Inicializar el pin del LED integrado como salida
  bot.sendMessage(CHAT_ID, "Bot iniciado", "");
}

// Función para enviar la lectura de humedad del suelo
void sendSoilMoistureReading() {
  int soilSensor = map(analogRead(moistPin), 693, 315, 0, 100);
  String response = "La humedad del suelo es " + String(soilSensor) + "%";
  bot.sendMessage(CHAT_ID, response, "");
}

// Función para manejar nuevos mensajes de Telegram
void handleNewMessages(int numNewMessages) {
  for (int i = 0; i < numNewMessages; i++) {
    String chat_id = String(bot.messages[i].chat_id);
    String text = bot.messages[i].text;

    if (text.equals("/humedad")) {
      sendSoilMoistureReading();
    } else {
      bot.sendMessage(chat_id, "Comando inválido. Usa /humedad", "");
    }
  }
}

void loop() {
  unsigned long currentMillis = millis();

  // Leer y mapear el valor del sensor de humedad del suelo
  if (currentMillis - previousMillis >= interval) {
    previousMillis = currentMillis;

    int soilSensor = map(analogRead(moistPin), 693, 315, 0, 100);

    Serial.print(soilSensor);
    Serial.println("% Humedad");

    // Comprobar si hay un cambio significativo en el nivel de humedad del suelo
    if (abs(soilSensor - lastSoilSensor) >= threshold) {
      if (soilSensor < 50) {
        digitalWrite(LED_BUILTIN, HIGH); // Encender el LED si la humedad es inferior al 50%
        bot.sendMessage(CHAT_ID, "Falta agua!!", "");
      } else {
        digitalWrite(LED_BUILTIN, LOW); // Apagar el LED si la humedad es superior o igual al 50%
        bot.sendMessage(CHAT_ID, "Humedad normal", "");
      }
      lastSoilSensor = soilSensor;
    }
  }

  // Manejar nuevos mensajes de Telegram
  int numNewMessages = bot.getUpdates(bot.last_message_received + 1);
  while (numNewMessages) {
    handleNewMessages(numNewMessages);
    numNewMessages = bot.getUpdates(bot.last_message_received + 1);
  }
}
