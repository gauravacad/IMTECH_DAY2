# Installing the ThingSpeak Library
To send sensor readings to ThingSpeak, we’ll use the thingspeak-arduino library. You can install this library through the Arduino Library Manager. Go to Sketch > Include Library > Manage Libraries… and search for “ThingSpeak” in the Library Manager. Install the ThingSpeak library by MathWorks.

<img width="786" height="443" alt="image" src="https://github.com/user-attachments/assets/d1ba0b8d-84bb-42da-a829-d009bfa87dea" />

# ThingSpeak – Getting Started

Go to [ThingSpeak](https://thingspeak.mathworks.com/ ) an click the “Get Started For Free” button to create a new account. This account is linked to a Mathworks account. So, if you already have a Mathworks account, you should log in with that account.

# Creating New Channel
1. After your account is ready, sign in, open the “Channels” tab and select “My Channels“.

<img width="438" height="258" alt="image" src="https://github.com/user-attachments/assets/5561f534-9c05-4886-8039-2cdc139c6162" />

2. Press the “New Channel” button to create a new channel.

<img width="453" height="326" alt="image" src="https://github.com/user-attachments/assets/31a588f1-70d8-4100-b4cf-8111f73e1797" />

3. Type a name for your channel and add a description.
4. In this example, we’ll just publish temperature. If you want to publish multiple readings (like humidity and pressure), you can enable more fields as you’ll see later in this tutorial.

<img width="740" height="500" alt="image" src="https://github.com/user-attachments/assets/5b805167-abd7-478f-a84c-1054fc86fc1f" />

5. Click the Save Channel button to create and save your channel.

<img width="313" height="91" alt="image" src="https://github.com/user-attachments/assets/2e1bff84-be21-4dad-8bfc-680fd28eaa86" />

6. Customizing Chart
The chart can be customized, go to your Private View tab and click on the edit icon.

<img width="690" height="693" alt="image" src="https://github.com/user-attachments/assets/41a62a53-86c2-465b-bd18-ae97752e74a8" />

7. You can give a title to your chart, customize the background color, x and y axis, and much more.

<img width="690" height="693" alt="image" src="https://github.com/user-attachments/assets/8318f2b1-dde4-45af-80ea-7457d5016f6c" />

8. API Key
To send values from the ESP32 to ThingSpeak, you need the Write API Key. Open the “API Keys”
tab and copy the Write API Key to a safe place because you’ll need it in a moment.

<img width="701" height="337" alt="image" src="https://github.com/user-attachments/assets/4219e3b8-5f19-4fd6-b697-d337c77ff17e" />

#  ESP32 Publish Sensor Readings to ThingSpeak – Code
```
/*
  Adapted from WriteSingleField Example from ThingSpeak Library (Mathworks)  
  Rui Santos & Sara Santos - Random Nerd Tutorials
  Complete project details at https://RandomNerdTutorials.com/esp32-thingspeak-publish-arduino/
  Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files.
  The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.
*/

#include <WiFi.h>
#include "ThingSpeak.h"
#include <Adafruit_BME280.h>
#include <Adafruit_Sensor.h>

const char* ssid = "REPLACE_WITH_YOUR_SSID";   // your network SSID (name) 
const char* password = "REPLACE_WITH_YOUR_PASSWORD";   // your network password

WiFiClient  client;

unsigned long myChannelNumber = X;
const char * myWriteAPIKey = "XXXXXXXXXXXXXXXX";

// Timer variables
unsigned long lastTime = 0;
unsigned long timerDelay = 30000;

// Variable to hold temperature readings
float temperatureC;
//uncomment if you want to get temperature in Fahrenheit
//float temperatureF;

// Create a sensor object
Adafruit_BME280 bme; //BME280 connect to ESP32 I2C (GPIO 21 = SDA, GPIO 22 = SCL)

void initBME(){
  if (!bme.begin(0x76)) {
    Serial.println("Could not find a valid BME280 sensor, check wiring!");
    while (1);
  }
}

void setup() {
  Serial.begin(115200);  //Initialize serial
  initBME();
  
  WiFi.mode(WIFI_STA);   
  
  ThingSpeak.begin(client);  // Initialize ThingSpeak
}

void loop() {
  if ((millis() - lastTime) > timerDelay) {
    
    // Connect or reconnect to WiFi
    if(WiFi.status() != WL_CONNECTED){
      Serial.print("Attempting to connect");
      while(WiFi.status() != WL_CONNECTED){
        WiFi.begin(ssid, password); 
        delay(5000);     
      } 
      Serial.println("\nConnected.");
    }

    // Get a new temperature reading
    temperatureC = bme.readTemperature();
    Serial.print("Temperature (ºC): ");
    Serial.println(temperatureC);
    
    //uncomment if you want to get temperature in Fahrenheit
    /*temperatureF = 1.8 * bme.readTemperature() + 32;
    Serial.print("Temperature (ºF): ");
    Serial.println(temperatureF);*/
    
    // Write to ThingSpeak. There are up to 8 fields in a channel, allowing you to store up to 8 different
    // pieces of information in a channel.  Here, we write to field 1.
    int x = ThingSpeak.writeField(myChannelNumber, 1, temperatureC, myWriteAPIKey);
    //uncomment if you want to get temperature in Fahrenheit
    //int x = ThingSpeak.writeField(myChannelNumber, 1, temperatureF, myWriteAPIKey);

    if(x == 200){
      Serial.println("Channel update successful.");
    }
    else{
      Serial.println("Problem updating channel. HTTP error code " + String(x));
    }
    lastTime = millis();
  }
}

```

# Channel Number and API key
- You need to insert the number of the channel that you’re publishing to. 
- If you only have one channel created in ThingSpeak, the channel number is 1.
- Otherwise, you can see the number of the channel on the Private View tab.
```
unsigned long myChannelNumber = 1;
```
Finally, you need to insert the Write API key you’ve gotten from the previous steps:
```
const char * myWriteAPIKey = "XXXXXXXXXXXXXXXX";
```
# Demonstration
<img width="731" height="525" alt="image" src="https://github.com/user-attachments/assets/cd503ec7-ca1f-439b-9f4a-feb98531142c" />

