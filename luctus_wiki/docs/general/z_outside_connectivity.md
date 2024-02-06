# Outside Connectivity

A garrysmod server sometimes needs outside connections to interact with the world.

An easy example would be: A MySQL database to sync donations with ingame ranks.

This page explains all the different methods of connectivity gmod provides.

## Inbuilt connectivity

Garrysmod itself only offers very limited connectivity.

The default HTTP library offers a GET and POST request via http.Fetch and http.Post respectively. This enables a garrysmod server to query outside data. It does NOT enable the server to sync in real-time with other applications.

Also: The sqlite database, located in garrysmod/sv.db, offers limited outside connectivity. A program could write into the database file and the garrysmod server could query this data after it has been inserted.

## Extra Connectivity

As already previously mentioned: There exist custom modules that can expand a garrysmod server to be able to connect with other services.

Mysqloo is one of those modules. It enables to use mysql instead of sqlite for database storage.

Other examples, which haven't been thoroughly tested by myself:

 - gm_redis , which enables the server to use redis servers for storage
 - gmod_luasocket , for tcp sockets to connect to anything
 - gmod-websockets , to extend the http module of gmod with websocket capabilities
