##### NOTE: This project has become something much larger than planned. The documentation below is 
##### outdated. This warning will be removed when it is updated.

Spring Server
=============

The goal of this project is to act as a springboard for applications that require the exchange of
data between a server and one or more clients over a TCP/IP network. Two programs are provided, client
and server, that together act as a communication framework upon which you can easily exchange data.

The server component can manage up to 1028 concurrent client connections.

Both the server and client components execute a simple handshake and utilize two-way heartbeat
polling to ensure that any unexpected disconnections are handled gracefully.

The heartbeat polling feature makes use of a simple command-response protocol that can be
customized and expanded upon.

Potential projects that may wish to use this repository as a jumping-off point range from a simple
poker game, to an infrastructure monitoring solution, to a Napster clone.

Since this project is coded in C, knowledge of C is required.

See 'Customizing' for information about how to customize these programs for your specific needs.

Installation
------------

To build both the server and client apps:
```
git clone https://github.com/bartobri/spring-server.git
cd spring-server
make
```

To build just the server app:
```
git clone https://github.com/bartobri/spring-server.git
cd spring-server
make server
```

To build just the client app:
```
git clone https://github.com/bartobri/spring-server.git
cd spring-server
make client
```

The resulting binaries will be located in the spring-server/bin directory. From there you can copy them
to your preferred bin directory.

Usage
-----

To run the server:

```
bin/server
```

The server listens on port 51717 by default. To override the port, use the `-p` option:

```
bin/server -p <port> 
```

To suppress server output, use the `-q` option:

```
bin/server -q
```

To run the client:

```
bin/client -h <hostname> -p <port>
```

Customizing
-----------

##### The Command-Response Protocol

The client and server programs both use a simple command-response protocol to communicate with each other. Each communication contains a command and an optional payload. Upon receipt of a command on either end, it can be evaluated and a response can be programmed (see 'Programming Command Responses').

It is expected that the first 4 bytes of any communication to contain the *command*. The command denotes the purpose of the communication and tells the recipient what response to take. It is up to you to create commands and program the response to them according to the needs of your application.

If you require more than 4 bytes for the command, that can be changed by editing the following macro in both the server and client source code:

```
#define COMMAND_SIZE     4
```

To summarize, any communication between the client and server should contain within the first 4 bytes (or as defined by COMMAND_SIZE) a command, followed by an optional payload containing ancillary data of arbitrary length.

This is the general structure of the communications between these two components.

##### The Kick-off

##### Programming Command Responses

In both the client and server source code there is a block of code labeled 'Evaluate command and respond'. This is where you will begin your customizations.

The first 3 lines of code in this section extract the *command* from the first 4 bytes of the input buffer.

Once data is received, the *command* is stored in the a character array called `command`. Using 'fooo' as an example, you can program a response like so:

```
if (strcmp(command, "fooo") == 0) {
	// Program response to command 'fooo' here.
}
```

You can use the existing command 'ping' as an example. When the server sends the heartbeat command 'ping' to the client, the client is programmed to response to the server with the same command, thus telling the server it is still alive.

```
if (strcmp(command, "ping") == 0)
	n = write(sockfd, "ping", 4);
```

Upon receipt of the ping command, the server stores the time so that it can keep track of how long it has been since it last received a heartbeat.

```
if (strcmp(command, "ping") == 0)
	cs_pointer->last_ping_time = time(NULL);
```

All data after the first 4 bytes can be used in any way you like. This data will be stored in the character array called `buffer`.



License
-------

This program is free software; you can redistribute it and/or modify it under the terms of the the
MIT License (MIT). See [LICENSE](LICENSE) for more details.
