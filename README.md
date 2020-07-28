ESP8266 Witty Cloud Board Demo
===========================================
<p class="post-meta">Jan 31, 2016 • Ady</p>
  </header>

  <article class="post-content">
    <p align="center">
<img src="https://web.archive.org/web/20160513182030im_/https://cloud.githubusercontent.com/assets/744810/12700003/5ac3cf0a-c786-11e5-93c3-5b146e50b894.jpg" alt="Witty Cloud Board" style="width:304px;">
</p>

<p>This is a followup post to last week's <a href="/web/20160513182030/http://adityatannu.com/blog/post/2016/01/24/ESP8266-Websockets-demo-using-NeoPixels.html">post</a> about using websockets to control NeoPixels. I've been slowly teaching myself HTML and JavaScript and in the process I came up with this simple project to demo all the peripherals on this new <a href="https://web.archive.org/web/20160513182030/http://www.aliexpress.com/item/ESP8266-serial-WIFI-Witty-cloud-Development-Board-ESP-12F-module/32516750994.html">Witty Cloud board</a>. It had a bunch or peripherals like RGB LED, LDR and a user button. The interface is based on this awesome <a href="https://web.archive.org/web/20160513182030/https://github.com/cnlohr/channel3">ESP8266 Analog Broadcast Television Interface</a> you might have seen recently. </p>

<h1>Here's a demo video</h1>

<p>Just go to http://Witty.local/ on any device with a browser.</p>

<p align="center">
<iframe src="https://web.archive.org/web/20160513182030if_/https://www.youtube.com/embed/rkWhuOZr1VU" allowfullscreen="" height="315" frameborder="0" width="560"></iframe>
</p>

<h1>Features</h1>

<ul>
<li>Websocket connection with ESP8266 board</li>
<li>LDR, RGB LED and button</li>
<li>HTML page hosted from ESP8266 SPIFFS</li>
</ul>

<h1>Source code</h1>

<h2><a href="https://web.archive.org/web/20160513182030/https://github.com/AdySan/WittyCloudTest/tree/master/WittyCloudTest/data">HTML Page</a></h2>

<p>This HTML page needs the whole <code>\data</code> folder to be uploaded to the SPIFFS. Just keep it in the same folder as your sketch as in this repo and use Tools &gt; ESP8266 Sketch Data Upload, which will copy all the files to the SPIFFS, and these will be accessible to your sketch. If you do not see this menu option, try installing the <a href="https://web.archive.org/web/20160513182030/https://github.com/esp8266/Arduino/blob/master/doc/filesystem.md#uploading-files-to-file-system">SPIFFS tool</a>. </p>

<p>You'll need to modify <a href="https://web.archive.org/web/20160513182030/https://github.com/AdySan/WittyCloudTest/blob/master/WittyCloudTest/data/menuinterface.js#L7">this</a> line in <code>menuinterface.js</code> to match you'r boards IP addres. Ideally this should work with <code>var connection = new WebSocket('ws://'+location.hostname+':81/', ['arduino']);</code> but today I didnt have any luck getting it to work. You can try, might work in your setup.</p>

<h2><a href="https://web.archive.org/web/20160513182030/https://github.com/AdySan/WittyCloudTest/blob/master/WittyCloudTest/WittyCloudTest.ino">Arduino Sketch</a></h2>

<p>This sketch is based on two examples <a href="https://web.archive.org/web/20160513182030/https://github.com/esp8266/Arduino/blob/master/libraries/ESP8266WebServer/examples/FSBrowser/FSBrowser.ino">FSBrowser.ino</a> and <a href="https://web.archive.org/web/20160513182030/https://github.com/Links2004/arduinoWebSockets/blob/master/examples/WebSocketServer/WebSocketServer.ino">WebSocketServer.ino</a>. It's basically running a HTTP server and Websocket server. The HTML/JS files are loaded from the SPIFFS. When it recieves certain commands on the websocket connection, it responds with a specific message. So this websocket connection is essentially working in a ping-pong fashion and it should work as fast as possible. I've seen it work consistently at around 230Hz, your mileage may vary. When testing at home with my Apple Airport Extreme, for some reason the connection keeps dropping every 30 sec to 1 min. To make it stable I had to add <a href="https://web.archive.org/web/20160513182030/https://github.com/AdySan/WittyCloudTest/blob/master/WittyCloudTest/WittyCloudTest.ino#L65">such</a> delay statements to throttle it down. I've been playing with WireShark to analyse this problem with <a href="https://web.archive.org/web/20160513182030/https://github.com/Links2004">Markus's</a> help, if you have any idea let me know! Basically here's what happens:</p>
<div class="highlight"><pre><code class="language-" data-lang="">[0] Disconnected!
[1] Disconnected!
[1] Connected from 0.0.0.0 url: 
[2] Disconnected!
[2] Connected from 0.0.0.0 url: 
[3] Disconnected!
[3] Connected from 0.0.0.0 url: 
[0] Connected from 192.168.1.10 url: /
[0] Got message: ping
LmacRxBlk:1
LmacRxBlk:1
LmacRxBlk:1
[0] Disconnected!
[1] Disconnected!
[1] Connected from 0.0.0.0 url: 
[2] Disconnected!
[2] Connected from 0.0.0.0 url: 
[3] Disconnected!
[3] Connected from 0.0.0.0 url:
</code></pre></div>
<p>The connections drops, the browser tries to reconnect but the IP is strangely 0.0.0.0. Becauce of these multiple connection requests, the ESP8266 throws a <code>LmacRxBlk:1</code>, saying its TCP buffer is full. I got it to work on a non-Apple router by <a href="https://web.archive.org/web/20160513182030/https://github.com/AdySan/WittyCloudTest/blob/master/WittyCloudTest/WittyCloudTest.ino#L343">disabling</a> Wifi sleep mode. But with an Apple Airport Extreme, the only thing that seems to work is throttling it down. I know for sure that this is not an ESP8266 issue because of <a href="https://web.archive.org/web/20160513182030/https://www.youtube.com/watch?v=8ISbmQTbjDI">this</a> video. Let me know if you have any ideas!</p>

<h1>Screenshots</h1>

<ul>
<li>HTML page rendered on desktop browser</li>
</ul>

<p align="center">
<img src="https://web.archive.org/web/20160513182030im_/http://adityatannu.com/images/Websockets3.png" alt="Fountain" align="middle">
</p>

<h1>Components</h1>

<ul>
<li><a href="https://web.archive.org/web/20160513182030/http://www.aliexpress.com/item/ESP8266-serial-WIFI-Witty-cloud-Development-Board-ESP-12F-module/32516750994.html">Witty Cloud board</a></li>
</ul>

<h1>Toolchain</h1>

<ul>
<li>Arduino IDE 1.6.7</li>
<li>ESP8266/Arduino Core (latest form git, but the 2.0.0.0 release should work too)</li>
<li><a href="https://web.archive.org/web/20160513182030/https://github.com/Links2004/arduinoWebSockets">arduinoWebSockets library</a></li>
</ul>

<h1>Known Issues</h1>

<ul>
<li>As explained above, the connection drops every now and then</li>
<li>Websocket connection doesn't work with mDNS or <code>location.hostname</code> for some reason</li>
<li>Cannot handle multiple clients</li>
</ul>

<h1>Future improvements</h1>

<ul>
<li>Try to use AngularJS and Bootstrap to make the pge look better as suggested by <a href="https://web.archive.org/web/20160513182030/https://twitter.com/nikil511">@nikil511</a>. </li>
<li>Not use jQuery if not absolutely necessary</li>
