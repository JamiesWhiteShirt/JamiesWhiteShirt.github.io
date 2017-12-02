---
title: "#2: Networking"
series: "The client-server architecture"
---
The primitive interface between logical sides imposes a certain data flow and certain restrictions.
This resource explains how logical sides interface with each other, and how you have to deal with that.

NOTE: For brevity, in this resource "client" refers to a logical client and "server" refers to a logical server unless stated otherwise.

# The universal interface: Networking
The connection between servers and clients is reduced to this networking interface, a universal interface.
It is universal in the sense networking is used for any kind of connection between clients and servers, including single player.

## Packets and connections
When a server and a client connects, they establish a **connection** where they communicate with **packets**.
This interface is quite primitive and can be described as such:

- A **packet** is a discrete message sent over a **connection** from either side to the other.
- A packet is serializable, meaning it can be represented as a stream of bytes.
- The connection is bidirectional. The server can send packets to the client and vice versa.
- The connection may be terminated by either side at any time.
- Packets are guaranteed to be received as long as the connection is not terminated.
- Packets are guaranteed to be received in the same order as they are sent.

## TCP/local connections
Minecraft uses a library called Netty to establish connections as either **TCP connections** or **local connections**.
_An integrated server_ will provide a local connection for the client, and TCP connections for remote clients on LAN.
_A dedicated server_ will only provide TCP connections for remote clients.

While TCP connections and local connections are more or less functionally equivalent, they differ in speed and reliability.
If you play video games you may be probably familiar with the terms **ping** and **packet loss**.
For local connections, the ping is virtually zero and packet loss is nonexistent.
For TCP connections however, ping is a significant factor, and packet loss causes significant slowdown.

## Why does all this matter?
Networking is the only interface Minecraft has to communicate between clients and servers to preserve compatibility with all kinds of servers.
It means that if your mod should communicate between sides, it must to go through this interface just like everything else.
Luckily, in most cases Minecraft has abstractions for this interface, but it helps to know what is happening in the background.

The fact that messages are serializable means messages cannot transmit actual objects.
They have to be reconstructed on the other end. This means you can't "cheat" and send mutable objects over the wire.

You should assume all connections are unreliable because they may be TCP connections.
This means accounting for both expected and unexpected latency, usually <100ms, sometimes >100ms, rarely >1s.
This brings the concept of **client side prediction** to user interaction design, which is highly prevalent in the base game's code.
