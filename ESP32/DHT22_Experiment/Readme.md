## ESP32 with DHT11/DHT22 Temperature and Humidity Sensor using Arduino IDE
<img width="138" height="300" alt="image" src="https://github.com/user-attachments/assets/7fd6be00-dd39-455f-a2f7-42f092db8ca2" />

<img width="830" height="346" alt="image" src="https://github.com/user-attachments/assets/9ec8c504-c3a2-4ada-b0ab-40761e19a64d" />

## Schematic Diagram
- Wire the DHT22 or DHT11 sensor to the ESP32 development board as shown in the following schematic diagram.

<img width="714" height="892" alt="image" src="https://github.com/user-attachments/assets/77d53e17-a082-4104-95d7-d1ab2ce56df9" />

In this example, we’re connecting the DHT data pin to GPIO 4. However, you can use any other suitable digital pin.

## Installing Libraries

- To read from the DHT sensor, we’ll use the DHT library from Adafruit. To use this library you also need to install the Adafruit Unified Sensor library. Follow the next steps to install those libraries.

- Open your Arduino IDE and go to Sketch > Include Library > Manage Libraries. The Library Manager should open.

- Search for “DHT” on the Search box and install the DHT library from Adafruit.

  <img width="750" height="422" alt="image" src="https://github.com/user-attachments/assets/ab544afd-41fe-4229-9646-2d4613c07625" />

  ## ESP32 Reading Temperature and Humidity Sketch

 ```
  // REQUIRES the following Arduino libraries:
 // - DHT Sensor Library: https://github.com/adafruit/DHT-sensor-library
// - Adafruit Unified Sensor Lib: https://github.com/adafruit/Adafruit_Sensor

#include "DHT.h"

#define DHTPIN 4     // Digital pin connected to the DHT sensor
// Feather HUZZAH ESP8266 note: use pins 3, 4, 5, 12, 13 or 14 --
// Pin 15 can work but DHT must be disconnected during program upload.

// Uncomment whatever type you're using!
//#define DHTTYPE DHT11   // DHT 11
#define DHTTYPE DHT22   // DHT 22  (AM2302), AM2321
//#define DHTTYPE DHT21   // DHT 21 (AM2301)

// Connect pin 1 (on the left) of the sensor to +5V
// NOTE: If using a board with 3.3V logic like an Arduino Due connect pin 1
// to 3.3V instead of 5V!
// Connect pin 2 of the sensor to whatever your DHTPIN is
// Connect pin 4 (on the right) of the sensor to GROUND
// Connect a 10K resistor from pin 2 (data) to pin 1 (power) of the sensor

// Initialize DHT sensor.
// Note that older versions of this library took an optional third parameter to
// tweak the timings for faster processors.  This parameter is no longer needed
// as the current DHT reading algorithm adjusts itself to work on faster procs.
DHT dht(DHTPIN, DHTTYPE);

void setup() {
  Serial.begin(9600);
  Serial.println(F("DHTxx test!"));

  dht.begin();
}

void loop() {
  // Wait a few seconds between measurements.
  delay(2000);

  // Reading temperature or humidity takes about 250 milliseconds!
  // Sensor readings may also be up to 2 seconds 'old' (its a very slow sensor)
  float h = dht.readHumidity();
  // Read temperature as Celsius (the default)
  float t = dht.readTemperature();
  // Read temperature as Fahrenheit (isFahrenheit = true)
  float f = dht.readTemperature(true);

  // Check if any reads failed and exit early (to try again).
  if (isnan(h) || isnan(t) || isnan(f)) {
    Serial.println(F("Failed to read from DHT sensor!"));
    return;
  }

  // Compute heat index in Fahrenheit (the default)
  float hif = dht.computeHeatIndex(f, h);
  // Compute heat index in Celsius (isFahreheit = false)
  float hic = dht.computeHeatIndex(t, h, false);

  Serial.print(F("Humidity: "));
  Serial.print(h);
  Serial.print(F("%  Temperature: "));
  Serial.print(t);
  Serial.print(F("°C "));
  Serial.print(f);
  Serial.print(F("°F  Heat index: "));
  Serial.print(hic);
  Serial.print(F("°C "));
  Serial.print(hif);
  Serial.println(F("°F"));
}

```

## Demonstration

<img width="726" height="376" alt="image" src="https://github.com/user-attachments/assets/92046348-e592-461a-8f14-fb7fb3e2020e" />

