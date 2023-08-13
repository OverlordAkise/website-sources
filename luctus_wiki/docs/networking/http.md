# HTTP (,Fetch,Post)

This page shows details and internals of the HTTP module.



# Introduction

Garry's Mod offers 3 different functions for sending HTTP requests: `HTTP`, `http.Fetch` and `http.Post`.  

The Fetch and Post functions are defined in LUA, because they are just wrappers for the `HTTP` function which is defined in C.

```lua
print(debug.getinfo(http.Fetch).short_src)
-- lua/includes/modules/http.lua
print(debug.getinfo(http.Post).short_src)...
-- lua/includes/modules/http.lua
print(debug.getinfo(HTTP).short_src)...
-- [C]
```

The Server and Client have the same http functions.

The http calls are "asynchronous", as they do not block the lua runtime while waiting for a response.  
This means that the success function in a http call will NOT be called directly after sending the request. It will be called when we receive an answer from the webserver, which could take from 10ms to 5s.

In comparison to the `net` library, which only sends data between server <-> player, a http call can send any data to any webserver in the internet.  
It can also take a lot longer to get a response, while in comparison a net message arrives nearly instantly.



Documentation in the official gmod wiki:

 - [https://wiki.facepunch.com/gmod/Global.HTTP](https://wiki.facepunch.com/gmod/Global.HTTP)
 - [https://wiki.facepunch.com/gmod/http.Fetch](https://wiki.facepunch.com/gmod/http.Fetch)
 - [https://wiki.facepunch.com/gmod/http.Post](https://wiki.facepunch.com/gmod/http.Post)



# Restrictions

**Warning**: All of these restrictions are only valid for a vanilla Garry's Mod server without any external modules.  
Many of these restrictions can be lifted if you use dll modules like `gmsv_websocket` or `gmsv_chttp`.


You can not have a websocket connection. This means your gmod server can not receive information from a webserver if it did not ask first.  
This problem currently has 2 solutions: Either you poll (aka. send a request to) the webserver every few seconds and check if new data arrived, or you install a websocket module and use that instead. 

All HTTP calls made from a default garrysmod server have a hardcoded HTTP user agent which can not be changed without external `.dll` modules.  
The user agent is (from my test) `Valve/Steam HTTP Client 1.0 GMod/13`.  
This user agent is (as of 2023) blocked by discord / cloudflare. If you try to send a discord webhook message from a gmod server you get a 403 error back.  
Source: [https://github.com/discord/discord-api-docs/issues/849](https://github.com/discord/discord-api-docs/issues/849)



# Example

An example call to a simple echo nginx server:

```lua
lua_run http.Fetch("http://s3.local/echo",function(b,s,h,c)print("Body:") print(b) print("---") print("Size",s) print("--") print("Headers:") PrintTable(h) print("---") print("Code",c) end)
```

Output:

```
Body:
remote_addr: 83.154.211.98
method: GET
scheme: http
remote_port: 51308
http_user_agent: Valve/Steam HTTP Client 1.0 GMod/13
content_type: 
remote_user: 
remote_addr: 83.154.211.98
host: s3.local
request_method: GET
query_string ():  ()
request_uri: /echo
request: GET /echo HTTP/1.1

---
Size	302
--
Headers:
Connection	=	keep-alive
Content-Length	=	302
Content-Type	=	text/plain
Date	=	Sun, 13 Aug 2023 15:00:53 GMT
Server	=	nginx/1.14.1
connection	=	keep-alive
content-length	=	302
content-type	=	text/plain
date	=	Sun, 13 Aug 2023 15:00:53 GMT
server	=	nginx/1.14.1
---
Code	200
```

The top part is the response from the server which shows the headers of our request from LUA, the bottom part is the response headers that the server sent to us.



# Example modules

To have websockets in lua you can use the GWSockets module by FredyH, the same creator as the mysqloo module.

Link: [https://github.com/FredyH/GWSockets](https://github.com/FredyH/GWSockets)

This allows your gmod server to have a constant http web connection open which allows data to flow bi-directional (aka. the server can asynchronously receive data).

For an example addon that uses this module you can visit my "luctus_multiserverchat" addon which lets 2 servers share the same radio messages via a websocket proxy server. This enables players to write a message to a different server via a simple chat command.  
Link: [https://github.com/OverlordAkise/darkrp-addons/tree/main/luctus_multiserverchat](https://github.com/OverlordAkise/darkrp-addons/tree/main/luctus_multiserverchat)
