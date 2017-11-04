+++
date = "2012-04-15T12:00:00"
draft = false
tags = ["WebSocket"]
title = "HTML5 之 WebSocket 通信协议介绍"
math = false
summary = """ """

[header]
image = ""
caption = ""

+++
更新：Chrome和Firefox的WebSocket协议已更新到最新版，不再支持旧版协议。本篇文章中的握手协议和数据报文已不再适用。

最近参与一个有关HTML5的比赛，要用到WebSocket，经过一些实验后简单总结一下WebSocket的基础用法。

WebSocket是HTML5的一种新的协议，它是实现了浏览器与服务器的双向通讯。

在 WebSocket API 中，浏览器和服务器只需要要做一个握手的动作，然后，浏览器和服务器之间就形成了一条快速通道。两者之间就直接可以数据互相传送。（这两小段介绍摘自维基百科）

### WebSocket协议
---

WebSocket的协议非常简单，这也是其最大的优点之一。

在客户端（如浏览器），可以利用JavaScript直接new WebSocket来实例化一个WebSocket对象，其参数格式为ws://yourdomain:port/path。WebSocket会根据这段字符串，发送Header到指定服务器端口，其数据格式大概如下：

```
GET / HTTP/1.1
Origin: http://hackecho.com 
Cookie: __utma=99as
Connection: Upgrade
Host: hackecho.com
Sec-WebSocket-Key: uRovscZjNol/umbTt5uKmw==
Upgrade: websocket Sec-WebSocket-Version: 13
```

此时服务端应该返回的信息是：

```
HTTP/1.    WebSocket Protocol Handshake
Date: Fri,     Feb     17:38:    GMT
Connection: Upgrade
Server: HackEcho Server
Upgrade: WebSocket
Access-Control-Allow-Origin: http://hackecho.com 
Access-Control-Allow-Credentials: true
Sec-WebSocket-Accept: rLHCkw/SKsO9GAH/ZSFhBATDKrU=
Access-Control-Allow-Headers: content-type
```

一旦握手成功建立连接，数据便可以通过WebSocket在服务器和客户端之间进行传送了。

### WebSocket事件
---

客户端在握手成功后，会触发WebSocket对象的onopen事件，告诉客户端连接已经成功建立了。

客户端的WebSocket对象一共绑定了四个事件：

1、onopen：连接建立时触发；

2、onmessage：收到服务端消息时触发；

3、onerror：连接出错时触发；

4、onclose：连接关闭时触发；

客户端连接的建立
这里提供一个例子来更好地理解客户端是怎样建立WebSocket连接的。例子来自于文末参考资料的第一篇文章。

将下列代码保存为一个html文件，如websocket.html，在浏览器中打开。页面会自动向服务器（由 http://www.websocket.org 提供）连接，发送一个消息并将返回的结果显示出来，最后断开连接。

通过其中的JavaScript代码即可看出用JS在本地建立连接的所有过程，比较简单，不再赘述，请大家看代码。

（不知怎么回事，如果去掉行号的代码缩进会乱掉，所以只能带上行号… 网上有很多去行号方法，大家可以Google之。）

```
<!DOCTYPE html>
<meta charset="utf-8" />
<title>
    WebSocket Test
</title>
<script language="javascript" type="text/javascript">
    var wsUri = "ws://echo.websocket.org/";
    var output;
    function init() {
        output = document.getElementById("output");
        testWebSocket();
    }
    function testWebSocket() {
        websocket = new WebSocket(wsUri);
        websocket.onopen = function(evt) {
            onOpen(evt)
        };
        websocket.onclose = function(evt) {
            onClose(evt)
        };
        websocket.onmessage = function(evt) {
            onMessage(evt)
        };
        websocket.onerror = function(evt) {
            onError(evt)
        };
    }
    function onOpen(evt) {
        writeToScreen("CONNECTED");
        doSend("WebSocket rocks");
    }
    function onClose(evt) {
        writeToScreen("DISCONNECTED");
    }
    function onMessage(evt) {
        writeToScreen('<span style="color: blue;">RESPONSE: ' + evt.data + '</span>');
        websocket.close();
    }
    function onError(evt) {
        writeToScreen('<span style="color: red;">ERROR:</span> ' + evt.data);
    }
    function doSend(message) {
        writeToScreen("SENT: " + message);
        websocket.send(message);
    }
    function writeToScreen(message) {
        var pre = document.createElement("p");
        pre.style.wordWrap = "break-word";
        pre.innerHTML = message;
        output.appendChild(pre);
    }
    window.addEventListener("load", init, false);
</script>
<h2>
    WebSocket Test
</h2>
<div id="output">
</div>

</html>
```
### 服务器端的实现
---

在将WebSocket应用到实际中时，我们往往需要自己的服务器来响应各种请求以实现更加丰富的功能。从前面的协议介绍部分来看，实现一个WebSocket服务器端并不难。但本着“不重复制造轮子”的原则，我们将基于开源的PHPWebSocket Project来二次开发自己的服务器。PHPWebSocket Project中已实现握手协议部分，其它的功能将可以由我们自由添加。这里只讨论握手协议部分和一个简单的响应函数。

将下列代码保存为server.php文件：

```
<?php
error_reporting(E_ALL);
set_time_limit(0);
ob_implicit_flush();

$master  = WebSocket("localhost",12345);
$sockets = array($master);
$users   = array();
$debug   = false;

while(true){
    $changed = $sockets;
    socket_select($changed,$write=NULL,$except=NULL,NULL);
    foreach($changed as $socket){
        if($socket==$master){
            $client=socket_accept($master);
            if($client<0){ console("socket_accept() failed"); continue; }
            else{ connect($client); }
        }
        else{
            $bytes = @socket_recv($socket,$buffer,2048,0);
            if($bytes==0){ disconnect($socket); }
            else{
                $user = getuserbysocket($socket);
                if(!$user->handshake){ dohandshake($user,$buffer); }
                else{ process($user,$buffer); }
            }
        }
    }
}

//---------------------------------------------------------------
function process($user,$msg){
    $action = unwrap($msg);
    say("< ".$action);
    send($user->socket,"lzy ".$action);
}

function send($client,$msg){
    say("> ".$msg);
    $msg = wrap($msg);
    socket_write($client,$msg,strlen($msg));
}

function WebSocket($address,$port){
    $master=socket_create(AF_INET, SOCK_STREAM, SOL_TCP)     or die("socket_create() failed");
    socket_set_option($master, SOL_SOCKET, SO_REUSEADDR, 1)  or die("socket_option() failed");
    socket_bind($master, $address, $port)                    or die("socket_bind() failed");
    socket_listen($master,20)                                or die("socket_listen() failed");
    echo "Server Started : ".date('Y-m-d H:i:s')."\n";
    echo "Master socket  : ".$master."\n";
    echo "Listening on   : ".$address." port ".$port."\n\n";
    return $master;
}

function connect($socket){
    global $sockets,$users;
    $user = new User();
    $user->id = uniqid();
    $user->socket = $socket;
    array_push($users,$user);
    array_push($sockets,$socket);
    console($socket." CONNECTED!");
}

function disconnect($socket){
    global $sockets,$users;
    $found=null;
    $n=count($users);
    for($i=0;$i<$n;$i++){
        if($users[$i]->socket==$socket){ $found=$i; break; }
    }
    if(!is_null($found)){ array_splice($users,$found,1); }
    $index = array_search($socket,$sockets);
    socket_close($socket);
    console($socket." DISCONNECTED!");
    if($index>=0){ array_splice($sockets,$index,1); }
}

function dohandshake($user,$buffer){
    console("\nRequesting handshake...");
    console($buffer);
    list($resource,$host,$origin,$strkey1,$strkey2,$data) = getheaders($buffer);
    console("Handshaking...");

    $pattern = '/[^\d]*/';
    $replacement = '';
    $numkey= preg_replace($pattern, $replacement, $strkey1);
    $numkey= preg_replace($pattern, $replacement, $strkey2);

    $pattern = '/[^ ]*/';
    $replacement = '';
    $spaces= strlen(preg_replace($pattern, $replacement, $strkey1));
    $spaces= strlen(preg_replace($pattern, $replacement, $strkey2));

    if ($spaces== || $spaces== || $numkey% $spaces!= || $numkey% $spaces!= 0) {
        socket_close($user->socket);
        console('failed');
        return false;
    }

    $ctx = hash_init('md5');
    hash_update($ctx, pack("N", $numkey1/$spaces1));
    hash_update($ctx, pack("N", $numkey2/$spaces2));
    hash_update($ctx, $data);
    $hash_data = hash_final($ctx,true);

    $upgrade  = "HTTP/1.WebSocket Protocol Handshake\r\n" .
    "Upgrade: WebSocket\r\n" .
    "Connection: Upgrade\r\n" .
    "Sec-WebSocket-Origin: " . $origin . "\r\n" .
    "Sec-WebSocket-Location: ws://" . $host . $resource . "\r\n" .
    "\r\n" .
    $hash_data;

    socket_write($user->socket,$upgrade.chr(0),strlen($upgrade.chr(0)));
    $user->handshake=true;
    console($upgrade);
    console("Done handshaking...");
    return true;
}

function getheaders($req){
    $r=$h=$o=null;
    if(preg_match("/GET (.*) HTTP/"   ,$req,$match)){ $r=$match[1]; }
    if(preg_match("/Host: (.*)\r\n/"  ,$req,$match)){ $h=$match[1]; }
    if(preg_match("/Origin: (.*)\r\n/",$req,$match)){ $o=$match[1]; }
    if(preg_match("/Sec-WebSocket-Key2: (.*)\r\n/",$req,$match)){ $key2=$match[1]; }
    if(preg_match("/Sec-WebSocket-Key1: (.*)\r\n/",$req,$match)){ $key1=$match[1]; }
    if(preg_match("/\r\n(.*?)\$/",$req,$match)){ $data=$match[1]; }
    return array($r,$h,$o,$key1,$key2,$data);
}

function getuserbysocket($socket){
    global $users;
    $found=null;
    foreach($users as $user){
        if($user->socket==$socket){ $found=$user; break; }
    }
    return $found;
}

function     say($msg=""){ echo $msg."\n"; }
function    wrap($msg=""){ return chr(0).$msg.chr(255); }
function  unwrap($msg=""){ return substr($msg,1,strlen($msg)-2); }
function console($msg=""){ global $debug; if($debug){ echo $msg."\n"; } }

class User{
    var $id;
    var $socket;
    var $handshake;
}

?>
```

运行php server.php命令启动服务器，这时服务器启动。

然后我们将下列代码保存为client.html文件：

```
<html>
<head>
<title>WebSocket</title>

<style>
html,body{font:normal 0.9em arial,helvetica;}
#log {width:440px; height:200px; border:1px solid #7F9DB9; overflow:auto;}
#msg {width:330px;}
</style>

<script>
var socket;

function init(){
    var host = "ws://localhost:12345/websocket/server.php";
    try{
        socket = new WebSocket(host);
        log('WebSocket - status '+socket.readyState);
        socket.onopen    = function(msg){ log("Welcome - status "+this.readyState); };
        socket.onmessage = function(msg){ log("Received: "+msg.data); };
        socket.onclose   = function(msg){ log("Disconnected - status "+this.readyState); };
    }
    catch(ex){ log(ex); }
    $("msg").focus();
}

function send(){
    var txt,msg;
    txt = $("msg");
    msg = txt.value;
    if(!msg){ alert("Message can not be empty"); return; }
    txt.value="";
    txt.focus();
    try{ socket.send(msg); log('Sent: '+msg); } catch(ex){ log(ex); }
}
function quit(){
    log("Goodbye!");
    socket.close();
    socket=null;
}

// Utilities
function $(id){ return document.getElementById(id); }
function log(msg){ $("log").innerHTML+="<br>"+msg; }
function onkey(event){ if(event.keyCode==13){ send(); } }
</script>

</head>
<body onload="init()">
<h3>WebSocket v2.00</h3>
<div id="log"></div>
<input id="msg" type="textbox" onkeypress="onkey(event)"/>
<button onclick="send()">Send</button>
<button onclick="quit()">Quit</button>
<div>Commands: hello, hi, name, age, date, time, thanks, bye</div>
</body>
</html>
```

这时在浏览器中打开client.html，即可实现一个简单的通过WebSocket连接到本地服务器然后发送消息、响应消息的应用。
