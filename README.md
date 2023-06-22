# Sensor de DHT22 con node-red
Este repositorio muestra como podemos programar una ESP32 con un DHT22 y ocupando la plataforma node-red y ahi poder visualizar en timpo y forma la Temperatura y la Húmedad y almismo tiempo graficar los resultados obtenidos.


### Descripción

La ```Esp32``` la utilizamos en un entorno de adquision de datos, lo cual en esta practica ocuparemos un sensor (```DHT22```) para medir de temperatura y humedad. Cabe aclarar que esta practica se usara en el simulador llamado [WOKWI](https://https://wokwi.com/).


## Material Necesario

Para realizar esta practica necesitas lo siguiente

- [WOKWI](https://https://wokwi.com/)
- Tarjeta ESP 32
- DHT22
- NODE-red (http://localhost:1880/#flow/214928d358894902)


## Instrucciones


### Requisitos previos

Para poder usar este repositorio necesitas entrar a la plataforma [WOKWI](https://https://wokwi.com/) y NODE-red (http://localhost:1880/#flow/214928d358894902) para esta ultima necvesitamos instalar un programa 


### Instrucciones de preparación de entorno 

1. Abrir la terminal de programación y colocar la siguente programación:

```
#include <ArduinoJson.h>
#include <WiFi.h>
#include <PubSubClient.h>
#define BUILTIN_LED 2
#include<max6675.h>
int CSK=13;
int CS=12;
int SO=14;
MAX6675 termopar(CSK,CS,SO);
// Update these with values suitable for your network.

const char* ssid = "Eventos_FCQeI";
const char* password = "2023FCQEI";
const char* mqtt_server = "52.57.167.175";
String username_mqtt="educatronicosiot";
String password_mqtt="12345678";

WiFiClient espClient;
PubSubClient client(espClient);
unsigned long lastMsg = 0;
#define MSG_BUFFER_SIZE  (50)
char msg[MSG_BUFFER_SIZE];
int value = 0;

void setup_wifi() {

  delay(10);
  // We start by connecting to a WiFi network
  Serial.println();
  Serial.print("Connecting to ");
  Serial.println(ssid);

  WiFi.mode(WIFI_STA);
  WiFi.begin(ssid, password);

  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

  randomSeed(micros());

  Serial.println("");
  Serial.println("WiFi connected");
  Serial.println("IP address: ");
  Serial.println(WiFi.localIP());
}

void callback(char* topic, byte* payload, unsigned int length) {
  Serial.print("Message arrived [");
  Serial.print(topic);
  Serial.print("] ");
  for (int i = 0; i < length; i++) {
    Serial.print((char)payload[i]);
  }
  Serial.println();

  // Switch on the LED if an 1 was received as first character
  if ((char)payload[0] == '1') {
    digitalWrite(BUILTIN_LED, LOW);   
    // Turn the LED on (Note that LOW is the voltage level
    // but actually the LED is on; this is because
    // it is active low on the ESP-01)
  } else {
    digitalWrite(BUILTIN_LED, HIGH);  
    // Turn the LED off by making the voltage HIGH
  }

}

void reconnect() {
  // Loop until we're reconnected
  while (!client.connected()) {
    Serial.print("Attempting MQTT connection...");
    // Create a random client ID
    String clientId = "ESP8266Client-";
    clientId += String(random(0xffff), HEX);
    // Attempt to connect
    if (client.connect(clientId.c_str(), username_mqtt.c_str() , password_mqtt.c_str())) {
      Serial.println("connected");
      // Once connected, publish an announcement...
      client.publish("outTopic", "hello world");
      // ... and resubscribe
      client.subscribe("inTopic");
    } else {
      Serial.print("failed, rc=");
      Serial.print(client.state());
      Serial.println(" try again in 5 seconds");
      // Wait 5 seconds before retrying
      delay(5000);
    }
  }
}

void setup() {
  pinMode(BUILTIN_LED, OUTPUT);     // Initialize the BUILTIN_LED pin as an output
  Serial.begin(115200);
  setup_wifi();
  client.setServer(mqtt_server, 1883);
  client.setCallback(callback);
}

void loop() {

int temperatura;
 temperatura=termopar.readCelsius();
delay(1000);

  if (!client.connected()) {
    reconnect();
  }
  client.loop();

  unsigned long now = millis();
  if (now - lastMsg > 2000) {
    lastMsg = now;
    //++value;
    //snprintf (msg, MSG_BUFFER_SIZE, "hello world #%ld", value);

    StaticJsonDocument<128> doc;

    doc["DEVICE"] = "ESP32";
    //doc["Anho"] = 2022;
    //doc["Empresa"] = "Educatronicos";
    doc["TEMPERATURA"] = temperatura;
    doc["AMPERAJE"] = random(0);
   

    String output;
    
    serializeJson(doc, output);

    Serial.print("Publish message: ");
    Serial.println(output);
    client.publish("Tamulbaout", output.c_str());
  }
}

```
2. Instalamos la libreria de **DHT sensor library for ESPx** y **LiquidCrystal I2C** como se muestra en la siguente imagen, dando clic en (Library Manager) y despues en el simbolo de (+)

![](https://github.com/Omarcollado23/PRACTICA-5DHT-LCD-ULTRASONICO/blob/main/libreria.jpg?raw=true)

3. Hacemos las conexiones el **ESP32** del sensor **HC-SR04**, el sensor **DHT22** y el **LCD** como se muestra en la siguiente imagen

![](https://github.com/Omarcollado23/PRACTICA-5DHT-LCD-ULTRASONICO/blob/main/conexiones.jpg?raw=true)

### Instrucciónes de operación

1. Iniciamos el simulador.
2. Visualizar los datos en el monitor serial.
3. Colocar la distancia dando *doble click* al sensor **HC-SR04** 

  

## Resultados

Cuando haya funcionado, la información obtenida del sensor **DHT22** y **HC-SR04** se arrojara en el LCD como se muestra en las siguentes imagenes.

![](https://github.com/Omarcollado23/PRACTICA-5DHT-LCD-ULTRASONICO/blob/main/datos%20distance.jpg?raw=true)
![](https://github.com/Omarcollado23/PRACTICA-5DHT-LCD-ULTRASONICO/blob/main/datos%202.jpg?raw=true)
![](https://github.com/Omarcollado23/PRACTICA-5DHT-LCD-ULTRASONICO/blob/main/datos%20hum-temp.jpg?raw=true)



# Créditos

Desarrollado por Ing. Omar Alejandro Collado Carriola

- [GitHub](https://github.com/Omarcollado23)