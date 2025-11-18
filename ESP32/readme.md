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
```
/*********
  Gaurav Puorhit
  Complete project details at at https://github.com/gauravacad/IMTECH_DAY2/edit/main/ESP32/readme.md
*********/

// Load Wi-Fi library
#include <WiFi.h>

// Replace with your network credentials
const char* ssid = "REPLACE_WITH_YOUR_SSID";
const char* password = "REPLACE_WITH_YOUR_PASSWORD";

// Set web server port number to 80
WiFiServer server(80);

// Variable to store the HTTP request
String header;

// Auxiliar variables to store the current output state
String output26State = "off";
String output27State = "off";

// Assign output variables to GPIO pins
const int output26 = 26;
const int output27 = 27;

// Current time
unsigned long currentTime = millis();
// Previous time
unsigned long previousTime = 0; 
// Define timeout time in milliseconds (example: 2000ms = 2s)
const long timeoutTime = 2000;

void setup() {
  Serial.begin(115200);
  // Initialize the output variables as outputs
  pinMode(output26, OUTPUT);
  pinMode(output27, OUTPUT);
  // Set outputs to LOW
  digitalWrite(output26, LOW);
  digitalWrite(output27, LOW);

  // Connect to Wi-Fi network with SSID and password
  Serial.print("Connecting to ");
  Serial.println(ssid);
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  // Print local IP address and start web server
  Serial.println("");
  Serial.println("WiFi connected.");
  Serial.println("IP address: ");
  Serial.println(WiFi.localIP());
  server.begin();
}

void loop(){
  WiFiClient client = server.available();   // Listen for incoming clients

  if (client) {                             // If a new client connects,
    currentTime = millis();
    previousTime = currentTime;
    Serial.println("New Client.");          // print a message out in the serial port
    String currentLine = "";                // make a String to hold incoming data from the client
    while (client.connected() && currentTime - previousTime <= timeoutTime) {  // loop while the client's connected
      currentTime = millis();
      if (client.available()) {             // if there's bytes to read from the client,
        char c = client.read();             // read a byte, then
        Serial.write(c);                    // print it out the serial monitor
        header += c;
        if (c == '\n') {                    // if the byte is a newline character
          // if the current line is blank, you got two newline characters in a row.
          // that's the end of the client HTTP request, so send a response:
          if (currentLine.length() == 0) {
            // HTTP headers always start with a response code (e.g. HTTP/1.1 200 OK)
            // and a content-type so the client knows what's coming, then a blank line:
            client.println("HTTP/1.1 200 OK");
            client.println("Content-type:text/html");
            client.println("Connection: close");
            client.println();
            
            // turns the GPIOs on and off
            if (header.indexOf("GET /26/on") >= 0) {
              Serial.println("GPIO 26 on");
              output26State = "on";
              digitalWrite(output26, HIGH);
            } else if (header.indexOf("GET /26/off") >= 0) {
              Serial.println("GPIO 26 off");
              output26State = "off";
              digitalWrite(output26, LOW);
            } else if (header.indexOf("GET /27/on") >= 0) {
              Serial.println("GPIO 27 on");
              output27State = "on";
              digitalWrite(output27, HIGH);
            } else if (header.indexOf("GET /27/off") >= 0) {
              Serial.println("GPIO 27 off");
              output27State = "off";
              digitalWrite(output27, LOW);
            }
            
            // Display the HTML web page
            client.println("<!DOCTYPE html><html>");
            client.println("<head><meta name=\"viewport\" content=\"width=device-width, initial-scale=1\">");
            client.println("<link rel=\"icon\" href=\"data:,\">");
            // CSS to style the on/off buttons 
            // Feel free to change the background-color and font-size attributes to fit your preferences
            client.println("<style>html { font-family: Helvetica; display: inline-block; margin: 0px auto; text-align: center;}");
            client.println(".button { background-color: #4CAF50; border: none; color: white; padding: 16px 40px;");
            client.println("text-decoration: none; font-size: 30px; margin: 2px; cursor: pointer;}");
            client.println(".button2 {background-color: #555555;}</style></head>");
            
            // Web Page Heading
            client.println("<body><h1>ESP32 Web Server</h1>");
            
            // Display current state, and ON/OFF buttons for GPIO 26  
            client.println("<p>GPIO 26 - State " + output26State + "</p>");
            // If the output26State is off, it displays the ON button       
            if (output26State=="off") {
              client.println("<p><a href=\"/26/on\"><button class=\"button\">ON</button></a></p>");
            } else {
              client.println("<p><a href=\"/26/off\"><button class=\"button button2\">OFF</button></a></p>");
            } 
               
            // Display current state, and ON/OFF buttons for GPIO 27  
            client.println("<p>GPIO 27 - State " + output27State + "</p>");
            // If the output27State is off, it displays the ON button       
            if (output27State=="off") {
              client.println("<p><a href=\"/27/on\"><button class=\"button\">ON</button></a></p>");
            } else {
              client.println("<p><a href=\"/27/off\"><button class=\"button button2\">OFF</button></a></p>");
            }
            client.println("</body></html>");
            
            // The HTTP response ends with another blank line
            client.println();
            // Break out of the while loop
            break;
          } else { // if you got a newline, then clear currentLine
            currentLine = "";
          }
        } else if (c != '\r') {  // if you got anything else but a carriage return character,
          currentLine += c;      // add it to the end of the currentLine
        }
      }
    }
    // Clear the header variable
    header = "";
    // Close the connection
    client.stop();
    Serial.println("Client disconnected.");
    Serial.println("");
  }
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



