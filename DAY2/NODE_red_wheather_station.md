# Node-Red – DHT11/DHT22 – Custom Weather Station Dashboard content
- Introduction
- Design
- Why I created my custom dashboard?
- Prerequisites
- Components/Parts
- Wiring/Schematic
- Node-Red Flow
- Web Page flow
- What about the HTML/CSS/Javascript code?
- Asynchronous HTTP endpoint flow
- DHT Manual Trigger
- How to run the project?
- Wrap Up

# Introduction 

### Wiring/Schematic

The image above is the wiring and schematic diagram for this project. You can replace the DHT22 with the DHT11 if that is the sensor you have.

<img width="960" height="540" alt="image" src="https://github.com/user-attachments/assets/2f1d5abe-6553-449a-afcf-080d0bb309e4" />

### Node-Red Flow

<img width="1147" height="715" alt="image" src="https://github.com/user-attachments/assets/85a81dd3-0e0f-4526-bb1a-cab8a15e7d60" />

The image above is the nodes associated with our Node-Red DHT22/DHT11 Custom Dashboard flow. It consists of the following sections:
- Web Page flow – This flow will show our custom dashboard page and consists of template nodes with the HMTL/CSS/Javascript code.
- Asynchronous HTTP endpoint flow – This flow will respond to an HTTP request “/sensorReadings” and will read our DHT22 node and return a JSON response
- DHT Manual Trigger – This flow will display the current temperature and humidity readings using the Inject and Debug nodes.

