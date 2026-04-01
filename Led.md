```c
/**
  @authors Gabriel A. Felippe C. Reginaldo S.
  */

#include <SPI.h>
#include <Ethernet.h>

#define led1 8
#define led2 7

byte mac[6] = { 0x90, 0xA2, 0xDA, 0xF5, 0xB1, 0xE9 };
;
EthernetServer server(80);

// aqui é prime

const char page[] PROGMEM = R"HTML(
  <!DOCTYPE html>
  <html lang="pt-BR">
    <head>
     <​meta charset="UTF-8">
     <​meta name="viewport" content="width=device-width, initial-scale=1.0">
      <title>Dale</title>
       <​style>
    body {
    font-family: sans-serif;
    text-align: center;
    }

    a {
    transition: .5s;
    background-color: rgb(116, 187, 9);
    padding: 6px;
    border: 1px solid rgb(115, 185, 10);
    border-radius: 3px;
    font-weight: bold;
    text-decoration: none;
    color: white;
    }

    .on {
    background-color: rgb(116, 187, 9);
    border: 1px solid rgb(115, 185, 10);
    }

    .on:hover {
    background-color: rgb(130, 201, 23);
    }

    .off {
    background-color: rgb(190, 23, 23);
    border: 1px solid rgb(190, 23, 23);
    }

    .off:hover {
    background-color: rgb(211, 31, 31);
    }
    <​/style>
    </head>
    <body>
    <h1>Super dale</h1>
    <p>Etapa 1 - Controle de dispositivos</p>

    <a class="on" href="/?led1-on">Ligar o dale</a>
    <a class="off" href="/?led1-off">Desligar</a>

    <br>
    <br>
    <br>

    <a class="on" href="/?led2-on">Ligar o LED 2</a>
    <a class="off" href="/?led2-off">Desligar o LED 2</a>
    </body>
    </html>

    )HTML";

void setup() {
  pinMode(led1, OUTPUT);
  pinMode(led2, OUTPUT);

  Serial.begin(9600);
  Ethernet.begin(mac);
  server.begin();

  Serial.println("Server web: ");

  Serial.print("Ip do server: ");
  Serial.println(Ethernet.localIP());
}

void loop() {
  EthernetClient client = server.available();

  if (client) {
    String request = "";

    // o comando é recebido caractere por caractere por limitações do Arduino
    while (client.available()) {
      char c = client.read();
      request += c;
    }

    // tratamento dos comandos recebidos vvvv
    if (request.indexOf("GET /?led1-on") >= 0) {
      digitalWrite(led1, HIGH);
    }

    if (request.indexOf("GET /?led2-on") >= 0) {
      digitalWrite(led2, HIGH);
    }

    if (request.indexOf("GET /?led1-off") >= 0) {
      digitalWrite(led1, LOW);
    }

    if (request.indexOf("GET /?led2-off") >= 0) {
      digitalWrite(led2, LOW);
    }

    // inicia-se o protocolo HTTP V
    client.println("HTTP/1.1 200 OK");
    client.println("Content-Type: text/html");
    client.println("Connection: close");
    client.println("");  // <- importante

    // entregar o código HTML
    client.print((__FlashStringHelper*)page);

    // encerrar a connection
    delay(1);  // só pra limpar o buffer da memória
    client.stop();
  }
}
```
