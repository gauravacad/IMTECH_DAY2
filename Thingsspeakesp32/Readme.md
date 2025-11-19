```
/**
   ESP32 + DHT22 + ThingSpeak Example using Wokwi
   By IoT Frontier
*/

#include <WiFi.h>
#include "DHTesp.h"
#include "ThingSpeak.h"

const int DHT_PIN = 15; // DHT22 sensor GPIO Pin
const char* WIFI_NAME = "Wokwi-GUEST"; // WiFi SSID
const char* WIFI_PASSWORD = ""; // WiFI Password
const int myChannelNumber = your channel; // ThingSpeak channel number
const char* myApiKey = "your key"; // ThingSpeak API key
const char* server = "api.thingspeak.com"; // ThingSpeak server address

// Create an instance of the DHTesp library
DHTesp dhtSensor; 
// Create a WiFi client object
WiFiClient client; 

void setup() {
  // Initialize the serial communication at a baud rate of 115200
  Serial.begin(115200);
  dhtSensor.setup(DHT_PIN, DHTesp::DHT22); // Initialize the DHT22 sensor
  WiFi.begin(WIFI_NAME, WIFI_PASSWORD); // Connect to the WiFi network
  while (WiFi.status() != WL_CONNECTED){
    delay(1000);
    Serial.println("Wifi not connected"); // Print a message if WiFi is not connected
  }
  Serial.println("Wifi connected !"); // Print a message if WiFi is connected
  Serial.println("Local IP: " + String(WiFi.localIP())); // Print the local IP address
  WiFi.mode(WIFI_STA); // Set the WiFi mode to station mode
  ThingSpeak.begin(client); // Initialize the ThingSpeak library
}

void loop() {
  // Read temperature and humidity from the DHT22 sensor
  TempAndHumidity  data = dhtSensor.getTempAndHumidity();
  // Set the value of field 1 in the ThingSpeak channel to the temperature
  ThingSpeak.setField(1,data.temperature);
  // Set the value of field 2 in the ThingSpeak channel to the humidity 
  ThingSpeak.setField(2,data.humidity); 

  // Write the data to the ThingSpeak channel
  int status = ThingSpeak.writeFields(myChannelNumber,myApiKey); 
  
  Serial.println("Temp: " + String(data.temperature, 2) + "°C"); // Print the temperature value with 2 decimal places
  Serial.println("Humidity: " + String(data.humidity, 1) + "%"); // Print the humidity value with 1 decimal place
  
  if(status == 200){
    Serial.println("Data pushed successfully"); // Print a message if the data was successfully pushed to ThingSpeak
  }else{
    Serial.println("Push error" + String(status)); // Print an error message with the HTTP status code if there was an error pushing the data
  }
  Serial.println("---"); // Print a separator line

  delay(10000); // Delay for 10 seconds
}
```
library help 

In the search bar, type "ThingSpeak" and find the library by MathWorks.

video help
https://m.youtube.com/watch?v=8JdOLIxw9Yc&time_continue=58&source_ve_path=NzY3NTg&embeds_referring_euri=https%3A%2F%2Fwww.google.com%2Fsearch%3Fq%3Ddht22%2Bon%2Bthings%2Bspeak%2Bwith%2Besp32%26oq%3Ddht22%2B%26gs_lcrp%3DEgZjaHJvbWUqBggAEEUYOzIGCAAQRRg7MgYIARBFGDsyC
