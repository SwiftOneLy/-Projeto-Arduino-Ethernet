```c

/**
 Arduino Web Server
 @authors Gabriel A.
 @authors Felippe C.
 @authors Reginaldo S.
*/

hashtag#include <SPI.h>
hashtag#include <Ethernet.h>

byte mac[6] = {0x90, 0xA2, 0xDA, 0x89, 0x60, 0xC8};
EthernetServer server(80);



const char page[] PROGMEM = R"HTML(
<!DOCTYPE HTML>
 <head>
 <​meta charset="UTF-8" />
 <​meta name="viewport" content="width=device-width, initial-scale=1.0">
 <title>DALE</title>
 <​style>
 body {
 text-align: center;
 font-family: sans-serif;
 }
 <​/style>
 </head>

 <body>
 <h1>BORA DE DALE</h1>
 </body>
</HTML>

)HTML";

void setup() {
 Serial.begin(9600);
 Ethernet.begin(mac);
 server.begin();

 Serial.println("Server web: ");

 Serial.print("Ip do server: ");
 Serial.println(Ethernet.localIP());
}

void loop() {
 EthernetClient client = server.available();

 if(client) {
 if(client.available()) {
 client.read();
 }

 // inicia-se o protocolo HTTP V
 client.println("HTTP/1.1 200 OK");
 client.println("Content-Type: text/html");
 client.println("Connection: close");
 client.println(""); // <- importante

 // entregar o código HTML
 client.print((__FlashStringHelper*)page);

 // encerrar a connection
 delay(1); // só pra limpar o buffer da memória
 client.stop();
 }
}
```
