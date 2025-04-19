#include <WiFi.h>
#include <HTTPClient.h>
#include "DHT.h"

#define DHTPIN 4
#define DHTTYPE DHT11
DHT dht(DHTPIN, DHTTYPE);

const char* ssid = "your_SSID";
const char* password = "your_PASSWORD";
String serverName = "http://api.thingspeak.com/update?api_key=YOUR_API_KEY";

void setup() {
  Serial.begin(115200);
  dht.begin();
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.println("Connecting to WiFi...");
  }
  Serial.println("Connected.");
}

void loop() {
  float temperature = dht.readTemperature();
  if (WiFi.status() == WL_CONNECTED) {
    HTTPClient http;
    String serverPath = serverName + "&field1=" + String(temperature);
    http.begin(serverPath.c_str());
    int httpResponseCode = http.GET();
    http.end();
  }
  delay(15000);
}
