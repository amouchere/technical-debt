---
title: Monitoring de température
date: 2020-11-28 14:47:30
tags: ["ESP8266", "DIY", "DHT22"]
---

L’[ESP8266](https://fr.wikipedia.org/wiki/ESP8266) est un circuit intégré à microcontrôleur avec connexion Wi-Fi développé par le fabricant chinois Espressif. 

Il est ici utilisé avec un capteur de température et d'humidité DHT22 (ou AM2302).


# Montage


![Esp8266](./photo1.jpg)


![DHT22](./photo2.jpg)

* La pin - de la sonde sur une pin Ground de l'esp.  
* La pin + de la sonde sur une pin 3V de l'esp.  
* La pin du milieu sur l'une des pin d1 de l'esp. Il faudra réutiliser le même numéro de pin dans le code ci-dessous. 

# Code
Dans ArduinoIDE, utiliser le type de carte esp8266 >NodeMCU 0.9.

>Penser aussi à charger les cartes et librairies esp8266: https://arduino.esp8266.com/stable/package_esp8266com_index.json

Plus d'info [ici](https://projetsdiy.fr/programmer-esp8266-ide-arduino-librairies-gpio-web-serveur-client/) 

```c
    #include <Arduino.h>
    #include <ESP8266WiFi.h>
    #include <ESP8266WiFiMulti.h>
    #include <ESP8266HTTPClient.h>
    #include <WiFiClient.h>
    #include "DHT.h"
    #define DHTTYPE DHT22

    ESP8266WiFiMulti WiFiMulti;

    // DHT Sensor
    uint8_t DHTPin = D1;
    // Initialize DHT sensor.
    DHT dht(DHTPin, DHTTYPE);

    float temperature;
    float humidity;

    const String location = "bureau_rdc";
    const char* ssid = "Livebox-*****";
    const char* password = "crAzYpssWD";
    const int delayInMin = 5;

    void setup() {

      Serial.begin(9600);
      // Serial.setDebugOutput(true);

      Serial.println();

      for (uint8_t t = 10; t > 0; t--) {
        Serial.printf("[SETUP] WAIT %d...\n", t);
        Serial.flush();
        delay(1000);
      }

      WiFi.mode(WIFI_STA);
      WiFiMulti.addAP(ssid, password);

      pinMode(DHTPin, INPUT);
      dht.begin();
    }

    void loop() {
      // wait for WiFi connection
      if ((WiFiMulti.run() == WL_CONNECTED)) {

        temperature = dht.readTemperature(); // Gets the values of the temperature
        humidity = dht.readHumidity(); // Gets the values of the humidity

        HTTPClient http;
        Serial.print("[HTTP] begin...\n");

        String url = "http://192.168.1.21:5000/";
        String postData = "{\"temperature\": \"" + String(temperature) + "\" , \"humidity\": \"" + String(humidity) + "\", \"location\": \"" + location + "\"}";

        Serial.print("Requesting url: ");
        Serial.println(url);
        Serial.println(postData);
        
        http.begin(url);
        http.addHeader("Content-Type", "application/json");

        auto httpCode = http.POST(postData);
        Serial.println(httpCode);

        if (httpCode < 0) {
          Serial.printf("[HTTP] POST... failed, error: %s\n", http.errorToString(httpCode).c_str());
        }

        http.end(); //Close connection Serial.println();
        Serial.println("closing connection");

      } else {
        Serial.println("Error in WiFi connection");
      }

      delay(delayInMin * 1000 * 60);
    }
```

Ce code permet de récupérer les données de température et d'humidité puis de les envoyer via HTTP sur un serveur distant. 