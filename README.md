
<h1>📡 Projeto Arduino Ethernet – Controle de LEDs via Rede Local</h1>

<h2>📖 Descrição</h2>
<p>Este projeto demonstra como controlar LEDs conectados a um Arduino com módulo Ethernet usando uma <strong>página web</strong>.</p>
<p>O Arduino cria um servidor HTTP na porta 80, permitindo que qualquer dispositivo na mesma rede local acesse a página e controle os LEDs com botões “ON” e “OFF”.</p>
<p>Além disso, o <strong>estado atual de cada LED</strong> é mostrado em tempo real na página:</p>
<ul>
<li>LED ligado → botão verde</li>
<li>LED desligado → botão vermelho</li>
</ul>
<p>A comunicação é feita via <code>fetch()</code> no JavaScript.</p>

<h2>🎯 Objetivos</h2>
<ul>
<li>Conectar o Arduino à rede via cabo Ethernet</li>
<li>Criar um servidor HTTP no Arduino</li>
<li>Controlar LEDs em tempo real via navegador</li>
<li>Visualizar o estado atual dos LEDs</li>
<li>Aprender conceitos básicos de IoT local</li>
</ul>

<h2>🛠️ Materiais Utilizados</h2>
<ul>
<li>Arduino (UNO, Mega, etc.)</li>
<li>Módulo Ethernet (W5100 ou W5500)</li>
<li>Cabo RJ45</li>
<li>LED x2</li>
<li>Resistores 220Ω ou 330Ω</li>
<li>Jumpers e protoboard</li>
<li>Roteador / Switch</li>
<li>Computador ou celular na mesma rede</li>
</ul>

<h2>🔌 1. Conexão do Hardware</h2>
<ul>
<li>Conecte o módulo Ethernet ao Arduino (pinos SPI: 10, 11, 12, 13)</li>
<li>Conecte o cabo RJ45 ao roteador</li>
<li>Conecte os LEDs com resistores nos pinos:</li>
</ul>

<table>
<tr><th>LED</th><th>Pino Arduino</th></tr>
<tr><td>LED1</td><td>8</td></tr>
<tr><td>LED2</td><td>6</td></tr>
</table>

<p>Certifique-se de conectar o GND do Arduino ao GND dos LEDs</p>

<h2>🌐 2. Configuração de Rede</h2>
<ul>
<li>Acesse o roteador e verifique a faixa de IP disponível</li>
<li>O Arduino obtém IP automaticamente via DHCP</li>
<li>(Opcional) Faça reserva de IP pelo MAC do Arduino</li>
<li>Salve as configurações</li>
</ul>

<h2>💻 3. Código do Arduino</h2>
<pre><code>#include &lt;SPI.h&gt;
#include &lt;Ethernet.h&gt;

#define led 8
#define led2 6

byte mac[6] = { 0x90, 0xA2, 0xDA, 0xF5, 0xB1, 0xE9 };
EthernetServer server(80);

bool ledState = false;
bool led2State = false;

const char page[] PROGMEM = R"HTML(
&lt;!DOCTYPE html&gt;
&lt;html lang=&quot;pt-BR&quot;&gt;
&lt;head&gt;
&lt;meta charset=&quot;UTF-8&quot;&gt;
&lt;meta name=&quot;viewport&quot; content=&quot;width=device-width, initial-scale=1.0&quot;&gt;
&lt;title&gt;Controle de LEDs&lt;/title&gt;
&lt;style&gt;
body { font-family: sans-serif; text-align: center; }
button { transition: .3s; padding: 10px 20px; font-weight: bold; margin: 5px; border-radius: 4px; border: none; color: white; cursor: pointer; }
.on { background-color: rgb(116,187,9); }
.off { background-color: rgb(190,23,23); }
&lt;/style&gt;
&lt;/head&gt;
&lt;body&gt;
&lt;h1&gt;Controle de LEDs&lt;/h1&gt;

&lt;p&gt;LED1&lt;/p&gt;
&lt;button id=&quot;led1-btn&quot; onclick=&quot;toggle('led1')&quot;&gt;ON&lt;/button&gt;

&lt;p&gt;LED2&lt;/p&gt;
&lt;button id=&quot;led2-btn&quot; onclick=&quot;toggle('led2')&quot;&gt;ON&lt;/button&gt;

&lt;script&gt;
async function toggle(led) {
    await fetch(&quot;/toggle-&quot; + led);
    await updateState();
}

async function updateState() {
    const res = await fetch(&quot;/status&quot;);
    const data = await res.json();

    document.getElementById(&quot;led1-btn&quot;).textContent = data.led1 ? &quot;ON&quot; : &quot;OFF&quot;;
    document.getElementById(&quot;led1-btn&quot;).className = data.led1 ? &quot;on&quot; : &quot;off&quot;;

    document.getElementById(&quot;led2-btn&quot;).textContent = data.led2 ? &quot;ON&quot; : &quot;OFF&quot;;
    document.getElementById(&quot;led2-btn&quot;).className = data.led2 ? &quot;on&quot; : &quot;off&quot;;
}

updateState();
setInterval(updateState, 2000);
&lt;/script&gt;
&lt;/body&gt;
&lt;/html&gt;
)HTML";

void setup() {
  pinMode(led, OUTPUT);
  pinMode(led2, OUTPUT);

  Serial.begin(9600);
  Ethernet.begin(mac);
  server.begin();

  Serial.println(&quot;Servidor Arduino Ethernet iniciado!&quot;);
  Serial.print(&quot;IP do Arduino: &quot;);
  Serial.println(Ethernet.localIP());
}

void loop() {
  EthernetClient client = server.available();
  if (client) {
    String request = &quot;&quot;;
    while (client.connected() &amp;&amp; client.available()) {
      char c = client.read();
      request += c;
    }

    if (request.indexOf(&quot;/toggle-led1&quot;) &gt; 0) {
      ledState = !ledState;
      digitalWrite(led, ledState ? HIGH : LOW);
    }
    else if (request.indexOf(&quot;/toggle-led2&quot;) &gt; 0) {
      led2State = !led2State;
      digitalWrite(led2, led2State ? HIGH : LOW);
    }
    else if (request.indexOf(&quot;/status&quot;) &gt; 0) {
      client.println(&quot;HTTP/1.1 200 OK&quot;);
      client.println(&quot;Content-Type: application/json&quot;);
      client.println(&quot;Connection: close&quot;);
      client.println();
      client.print(&quot;{&quot;);
      client.print(&quot;\&quot;led1\&quot;:&quot;);
      client.print(ledState ? &quot;true&quot; : &quot;false&quot;);
      client.print(&quot;,&quot;);
      client.print(&quot;\&quot;led2\&quot;:&quot;);
      client.print(led2State ? &quot;true&quot; : &quot;false&quot;);
      client.println(&quot;}&quot;);
      client.stop();
      return;
    }
    else {
      client.println(&quot;HTTP/1.1 200 OK&quot;);
      client.println(&quot;Content-Type: text/html&quot;);
      client.println(&quot;Connection: close&quot;);
      client.println();
      client.print((__FlashStringHelper*)page);
    }

    delay(1);
    client.stop();
  }
}
</code></pre>

<h2>📱 4. Acessando o Servidor Web</h2>
<ul>
<li>Conecte seu computador ou celular à mesma rede que o Arduino</li>
<li>Abra o navegador e acesse: <code>http://IP_DO_ARDUINO</code></li>
<li>Exemplo: <code>http://192.168.0.100</code></li>
<li>Clique nos botões “ON” e “OFF” para controlar os LEDs</li>
<li>Os botões mudam de cor conforme o estado real do LED</li>
</ul>

<h2>✅ Resultados Esperados</h2>
<ul>
<li>LED1 e LED2 respondem aos cliques na página web</li>
<li>Estado dos LEDs é mostrado em tempo real</li>
<li>LEDs permanecem no estado selecionado</li>
<li>Comunicação confiável via rede local</li>
</ul>

<h2>⚡ Possíveis Melhorias</h2>
<ul>
<li>Adicionar mais LEDs ou sensores</li>
<li>Criar painel de controle completo estilo mini-dashboard IoT</li>
<li>Criar API REST para controle remoto por apps externos</li>
<li>Otimizar atualização do estado sem polling constante</li>
</ul>

<h2>🧪 Troubleshooting</h2>
<ul>
<li>Certifique-se de que os LEDs estão nos pinos corretos e com GND conectado</li>
<li>Verifique cabos RJ45 e alimentação do Arduino</li>
<li>Confira se o Arduino recebeu um IP válido via DHCP</li>
<li>Reinicie Arduino ou roteador se necessário</li>
</ul>

</body>
</html>
