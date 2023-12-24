# Source Engine Networking

This page describes a few networking details of the source engine in Garry's Mod.


## Basic infos

Source engine only uses UDP for transport and **never** TCP.  
In all my tests the whole game logic and traffic went entirely via UDP.  
Only the initial "Assigning gameserver on Steam" part near the end of the server start process is done via TCP. Your server connects to a steam server on port 27020 and retrieves an (anonymous) "gameserver Steam ID".

The server doesn't actively use the client or sourcetv port.  
The client can decide on its own client port, which means one player can have the default 27005 port and another player can connect to the server via (for example) port 9329.

The rate of packets is (nearly) constant and equal to the rate of the tickrate.  
E.g. on a 33tick server the rate of packets from the server to the client and from client to server is 33 packets/second, which totals to 66 recv/send per second per player.

The server port (default 27015) receives game traffic and "multiplayer browser" traffic.  
If someone in the multiplayer server browser clicks on your server it actually sends a UDP packet to your server which it responds to with (for example) servername, playercount, playernames, and more.

**Attention:** The following tcpdump examples have been captured on a 32bit gmod server on linux with a 32bit gmod client on windows 10.

The first UDP packet of a "join attempt" is always 20 bytes in length, and the server response 39 bytes long. The third packet from the client is, if he is not kicked for "wrong password", ~600 bytes in size.

An example of a connection attempt, captured with tcpdump:

```
22:36:50.601111 eth0  In  IP client.27005 > server.27015: UDP, length 20
22:36:50.607826 eth0  Out IP server.27015 > client.27005: UDP, length 39
22:36:50.621676 eth0  In  IP client.27005 > server.27015: UDP, length 299
22:36:50.631773 eth0  Out IP server.27015 > client.27005: UDP, length 20
22:36:50.677076 eth0  In  IP client.27005 > server.27015: UDP, length 601
22:36:50.683806 eth0  Out IP server.27015 > client.27005: UDP, length 184
22:36:50.689108 eth0  Out IP server.27015 > client.27005: UDP, length 16
22:36:50.689146 eth0  Out IP server.27015 > client.27005: UDP, length 16
22:36:50.689168 eth0  Out IP server.27015 > client.27005: UDP, length 16
22:36:50.689179 eth0  Out IP server.27015 > client.27005: UDP, length 16
```

During this time (and mapchanges) the server->client packet size can reach up to 1045, while the client mostly sends 16 byte packets after the first 3 packets.

The whole connection process only takes 3 client packets which are 20, 299 and 601 bytes in size (according to my tests).

After that a normal "tick" (=one packet in, one packet out) with one player can look like this:

```
22:41:02.828348 eth0  In  IP client.27005 > server.27015: UDP, length 57
22:41:02.839779 eth0  Out IP server.27015 > client.27005: UDP, length 145
```

## Server udp packet size

If you noclip around the map and fly into new areas then the server has to send you the information of every Entity in your (new) PVS. This means the server has to send more than usual.  
In my case these were 1260 byte packets, 3 in a row, and then a 919 byte packet. This is because of `net_maxfragments / net_maxroutable` being set to 1260 by default. (Or because of `sv_usermessage_maxsize` being set to 1024)


## Client udp packet size

The player UDP packet size when idle is around 52 bytes and gets up to 130 bytes if the player jumps and looks around.  

If you send a net message it simply expands the UDP packet that contains your movement data and adds it together.  
This means that you do not send an extra packet when sending big net messages.  
According to my tests it adds (max) 256 bytes on a client network package, which makes the size 300 if idle (~52 bytes from idle and 256 from net message data) and ~350 bytes if moving (~100 bytes from movement and 256 net).  
If you send a big net-message in lua then it adds these 256 bytes to many of your normal tick UDP packets. This means a large net message can take a while to be send to the server. You can follow this progress if you enable your net graph ingame (`net_graph 3`) and follow the green line in the middle of the number display.

Funnily enough, the console variable for this (`lua_networkvar_bytespertick` , default=256) doesn't work if you set it on client and server. Or atleast it doesn't work if you set it manually on a running server, because the udp size stays the same as described above no matter what value you set.

Talking via voice chat can add anywhere between 100 and 600 bytes to the packet size, which would make a >900 byte packet (~50 bytes for movement, 256 bytes for net message, ~600 bytes for voice chat) possible.

In Summary: Everything you do simply gets added to the already constant 33ticks/second udp packets.
