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

## 
