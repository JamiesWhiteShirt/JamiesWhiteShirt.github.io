---
title: "The client-server architecture"
---
Minecraft applies a universal client-server architecture in both multi player and single player.
Sometimes it may be unclear how it works, how to deal with it, and what are good practices.
This series provides an in-depth overview of the way it works and what it means for developers.

Multiple computers need to agree on a truth about the Minecraft game state and the way it changes.
This is why the client-server architecture is applied.
The server is the single, universal source of truth that all clients agree on.

Because of how central the client-server architecture is to Minecraft, it is necessary to be familiar with the way it works.
