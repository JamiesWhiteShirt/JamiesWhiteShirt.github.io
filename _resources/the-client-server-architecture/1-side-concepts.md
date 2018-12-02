---
title: "#1: Side concepts"
series: "The client-server architecture"
---
When discussing Minecraft's architecture, the terms "server" and "client" may be ambiguous.
This resource explains some necessary concepts to get started.

# Logical sides - The separation of concerns
The Minecraft game has two **logical sides**. A logical side refers to a specific part of the game logic that has certain responsibilities.
The two logical sides are separated but connected through a very primitive interface and work in unison.
We call this a "logical" side because it describes what kind of logic is applied.

First, some definitions:

- **The game state** contains things such as entities, tile entities, the placement of blocks and various properties of the world.
- **Game events** are events such as monsters spawning, blocks changing and entities moving. Game events lead to changes in the game state.
- **Player actions** are actions such as walking, jumping, hitting blocks and clicking buttons in GUIs. Player actions lead to game events.

**A logical server** is responsible for being the authoritative source of truth about the game state and game events.
- Is connected to one or multiple **logical clients**.
- Holds a complete representation of the game state. Connected clients are selectively informed about this state.
- Decides which, when, and how game events happen. Connected clients are selectively informed about it these events.
- Receives player actions from clients. Decides the effective outcome of the actions.

**A logical client** is responsible for collecting keyboard/mouse input and providing video/audio output.
- Is connected to one **logical server**.
- Holds an incomplete representation of the game state. Only knows for certain what the server has informed it about.
- Is informed by the server about some game events that happen when they happen.
- Informs the server of its player actions. The effective outcome of player actions can only be predicted by the client.


# Physical sides - Minecraft distributions
The use of the word **logical** is a necessity for disambiguation. A logical side is not the same as a **physical side**.
A physical side refers to a specific type of Minecraft distribution.
We call this a "physical" side because it describes what systems are "physically" present in the software environment.

The concepts are still related. Logical sides are hosted as in the table below:

|                     | Logical server        | Logical client |
|:-------------------:|:---------------------:|:--------------:|
| **Physical client** | Integrated/LAN server | Yes            |
| **Physical server** | Dedicated server      | No             |


**A physical client** is a Minecraft client distribution. It hosts an integrated server when in singleplayer or acting as a LAN server. When connected to a remote server, the physical client does not host the logical server. The logical client can connect to the local logical server, or connect remotely to either a **physical server** or another **physical client** via LAN.

**A physical server** is a Minecraft server distribution. It only hosts a dedicated server that logical clients from **physical clients** can connect to.

# The universal client-server architecture
It should be obvious that this architecture is not strictly necessary in a single player situation.
However, Minecraft applies this architecture universally. The client is either connected to an integrated server or a remote server.

The universal client-server architecture turns out to have some very useful properties.
It allows reusing a significant amount of code across the Minecraft client and the Minecraft server distribution.
In fact, the physical client and physical server are different distributions of the same codebase.
