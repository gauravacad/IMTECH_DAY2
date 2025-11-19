# ESP32 Web Server – Arduino IDE

<img width="1024" height="576" alt="image" src="https://github.com/user-attachments/assets/f4a15691-c590-4787-93a2-6a70858242af" />

## Project Overview
- Before going straight to the project, it is important to outline what our web server will do, so that it is easier to follow the steps later on.
- The web server you’ll build controls two LEDs connected to the ESP32 GPIO 26 and GPIO 27;
- You can access the ESP32 web server by typing the ESP32 IP address on a browser in the local network;
- By clicking the buttons on your web server you can instantly change the state of each LED.
- This is just a simple example to illustrate how to build a web server that controls outputs, the idea is to replace those LEDs with a relay, or any other electronic components you want.
- Component required
  
<img width="750" height="500" alt="image" src="https://github.com/user-attachments/assets/29a7054b-0b51-42bb-a0ab-83a8ac620c22" />

## Wiring the Schematic
- Start by building the circuit. Connect two LEDs to the ESP32 as shown in the following schematic diagram – one LED connected to `GPIO 26`, and the other to `GPIO 27`.
- Note: We’re using the ESP32 DEVKIT DOIT board with 36 pins. Before assembling the circuit, make sure you check the pinout for the board you’re using.
- 
<img width="984" height="700" alt="image" src="https://github.com/user-attachments/assets/0c6a5f4b-4f13-42e6-951d-ec1da1610492" />

## ESP32 Web Server Code
 <img width="856" height="484" alt="image" src="https://github.com/user-attachments/assets/d3bcd23e-3b8d-417d-b318-f91e7909be66" />
```
/*********
  Gaurav Puorhit
  Complete project details at at https://github.com/gauravacad/IMTECH_DAY2/edit/main/ESP32/readme.md
  check the ip 192.168.5.100 that is your ip in console
 

*********/
#include <WiFi.h>
#include <WiFiClient.h>
#include <WebServer.h>
#include <uri/UriBraces.h>

#define WIFI_SSID "Iphone"
#define WIFI_PASSWORD "Sushant@2012"
// Defining the WiFi channel speeds up the connection:
#define WIFI_CHANNEL 6

WebServer server(80);

const int LED1 = 4;
const int LED2 = 27;

bool led1State = false;
bool led2State = false;

void sendHtml() {
  String response = R"(
    <!DOCTYPE html><html>
      <head>
        <title>ESP32 Web Server Demo</title>
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <style>
          html { font-family: sans-serif; text-align: center; }
          body { display: inline-flex; flex-direction: column; }
          h1 { margin-bottom: 1.2em; } 
          h2 { margin: 0; }
          div { display: grid; grid-template-columns: 1fr 1fr; grid-template-rows: auto auto; grid-auto-flow: column; grid-gap: 1em; }
          .btn { background-color: #5B5; border: none; color: #fff; padding: 0.5em 1em;
                 font-size: 2em; text-decoration: none }
          .btn.OFF { background-color: #333; }
        </style>
      </head>
            
      <body>
        <h1>ESP32 Web Server</h1>

        <div>
          <h2>LED 1</h2>
          <a href="/toggle/1" class="btn LED1_TEXT">LED1_TEXT</a>
          <h2>LED 2</h2>
          <a href="/toggle/2" class="btn LED2_TEXT">LED2_TEXT</a>
        </div>
      </body>
    </html>
  )";
  response.replace("LED1_TEXT", led1State ? "ON" : "OFF");
  response.replace("LED2_TEXT", led2State ? "ON" : "OFF");
  server.send(200, "text/html", response);
}

void setup(void) {
  Serial.begin(115200);
  pinMode(LED1, OUTPUT);
  pinMode(LED2, OUTPUT);

  WiFi.begin(WIFI_SSID, WIFI_PASSWORD, WIFI_CHANNEL);
  Serial.print("Connecting to WiFi ");
  Serial.print(WIFI_SSID);
  // Wait for connection
  while (WiFi.status() != WL_CONNECTED) {
    delay(100);
    Serial.print(".");
  }
  Serial.println(" Connected!");

  Serial.print("IP address: ");
  Serial.println(WiFi.localIP());

  server.on("/", sendHtml);

  server.on(UriBraces("/toggle/{}"), []() {
    String led = server.pathArg(0);
    Serial.print("Toggle LED #");
    Serial.println(led);

    switch (led.toInt()) {
      case 1:
        led1State = !led1State;
        digitalWrite(LED1, led1State);
        break;
      case 2:
        led2State = !led2State;
        digitalWrite(LED2, led2State);
        break;
    }

    sendHtml();
  });

  server.begin();
  Serial.println("HTTP server started");
}

void loop(void) {
  server.handleClient();
  delay(2);
}

```

### Setting Your Network Credentials
```
// Replace with your network credentials
const char* ssid     = "REPLACE_WITH_YOUR_SSID";
const char* password = "REPLACE_WITH_YOUR_PASSWORD";

```
### Uploading the Code
Now, you can upload the code and and the web server will work straight away. Follow the next steps to upload code to the ESP32:
1) Plug your `ESP32 board` in your computer;
2) In the Arduino IDE select your board in Tools > Board (in our case we’re using the ESP32 DEVKIT DOIT board);


<img width="614" height="520" alt="image" src="https://github.com/user-attachments/assets/98493acd-5716-4c4f-9754-d549037793df" />

3) Select the COM port in Tools > Port
   
<img width="490" height="386" alt="image" src="https://github.com/user-attachments/assets/62367175-1260-44dd-993a-d65c500a7c5d" />

4) Wait for the “Done uploading” message.

<img width="671" height="388" alt="image" src="https://github.com/user-attachments/assets/0960cbf6-d5ed-49d9-a021-e0f12c858a9b" />

5) Finding the ESP IP Address

<img width="739" height="222" alt="image" src="https://github.com/user-attachments/assets/93fcbbf1-7ca3-4c38-97c6-136498602eff" />

6) To access the web server, open your browser, paste the ESP32 IP address, and you’ll see the following page. In our case it is 192.168.1.135.

<img width="623" height="435" alt="image" src="https://github.com/user-attachments/assets/508e6021-a98d-4e42-a5ee-70f6bfc76cfc" />

### Testing the Web Server

- Now you can test if your web server is working properly. Click the buttons to control the LEDs.
<img width="750" height="421" alt="image" src="https://github.com/user-attachments/assets/2c683280-6396-48ce-ac62-2d9bbdc90737" />

- When the ESP32 receives that request, it turns the LED attached to GPIO 26 ON and updates its state on the web page.

<img width="623" height="435" alt="image" src="https://github.com/user-attachments/assets/f290d0e9-6d02-4aad-9fcc-0d5790067654" />



