# 📡 Projeto Arduino Ethernet – Comunicação em Rede Local

## 📖 Descrição

Este projeto demonstra a comunicação entre um Arduino com módulo Ethernet e dispositivos em uma rede local.

O Arduino é configurado para obter um endereço IP automaticamente (DHCP) e disponibilizar um servidor HTTP básico na porta 80.

---

## 🎯 Objetivos

- Conectar o Arduino à rede via cabo Ethernet  
- Obter um endereço IP automaticamente (DHCP)  
- Permitir comunicação com dispositivos da mesma rede  
- Validar conexão via monitor serial ou navegador  

---

## 🛠️ Materiais Utilizados

- Arduino  
- Módulo Ethernet (W5100 ou W5500)  
- Cabo de rede (RJ45)  
- Roteador  
- Computador para programação  
- Celular ou outro dispositivo na mesma rede  

---

## 🔌 1. Conexão do Hardware

- Conecte o módulo Ethernet ao Arduino  
- Conecte o cabo RJ45 do módulo ao roteador  
- Verifique se os LEDs da porta Ethernet estão acesos  

**Pinos utilizados:** 4, 10, 11, 12 e 13  

---

## 🌐 2. Configuração de Rede

1. Acesse o roteador (ex: 192.168.0.1)  
2. Vá até DHCP / Reserva de IP  
3. Identifique o Arduino pelo MAC  
4. (Opcional) Reserve um IP fixo  
5. Salve as configurações  

---

## 💻 3. Código do Arduino

```cpp
/**
 Arduino Ethernet
 @author Gabriel A., Felippe Buxa e Reginaldo Soares
*/

// OBS: Pinos utilizados são os 4, 10, 11, 12 e 13.

#include <SPI.h>
#include <Ethernet.h>

byte mac[6] = { 0x90, 0xA2, 0xDA, 0x57, 0xB0, 0x2D };

EthernetServer server(80); // Porta HTTP

void setup() {
  Serial.begin(9600);

  // Inicializa com DHCP
  Ethernet.begin(mac);

  server.begin();

  Serial.println("Arduino Ethernet Shield");

  Serial.print("IP: ");
  Serial.println(Ethernet.localIP());

  Serial.print("Máscara de sub-rede: ");
  Serial.println(Ethernet.subnetMask());

  Serial.print("Gateway: ");
  Serial.println(Ethernet.gatewayIP());

  Serial.print("DNS: ");
  Serial.println(Ethernet.dnsServerIP());
}

void loop() {
}
```

---

## 📱 4. Conectar o Dispositivo

- Conecte o celular ou computador ao mesmo Wi-Fi do roteador  
- Certifique-se de que está na mesma faixa de IP  

---

## 📡 5. Teste de Comunicação

### ✔️ Via Monitor Serial

Após enviar o código:

- Abra o Monitor Serial (9600 baud)  
- Verifique o IP atribuído ao Arduino  

---

### ✔️ Via Navegador

No celular ou computador, acesse:

```
http://IP_DO_ARDUINO
```

Exemplo:

```
http://192.168.0.100
```

---

### ✔️ Ping (opcional)

Use apps como PingTools:

```
ping 192.168.X.X
```

**Observação:** nem todo Arduino responde a ping.

---

## ⚠️ Observações Importantes

- O Arduino está configurado via DHCP  
- O IP pode mudar se não houver reserva no roteador  
- Certifique-se de usar um MAC válido e único  
- O servidor HTTP está ativo na porta 80  

---

## 🧪 6. Troubleshooting

Se não funcionar:

- Verifique cabos e conexões  
- Confira se os LEDs da Ethernet estão acesos  
- Veja o IP no Monitor Serial  
- Reinicie Arduino e roteador  
- Verifique firewall da rede  
- Confirme que os dispositivos estão na mesma rede  

---

## ✅ Resultados Esperados

- Arduino conectado à rede com IP válido  
- Informações de rede exibidas no Serial Monitor  
- Acesso ao IP do Arduino via navegador  
- Comunicação estabelecida na rede local  

---

## 🚀 Possíveis Melhorias

- Criar página web no Arduino  
- Controlar LEDs via navegador  
- Ler sensores e enviar dados pela rede  
- Criar API simples (REST)  
