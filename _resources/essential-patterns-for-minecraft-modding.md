---
title: "Essential patterns for Minecraft modding"
---

A collection of architectural patterns that are essential for creating a well written and maintainable mod.

# Re-use vanilla code the right way
Your primary goal when re-using vanilla behavior should be to simply call the vanilla code as opposed to copying its code.
Copying code adds the burden of maintaining and understanding it.
When done in excess, you risk having to do a lot of work to update your mod.

When CTRL-C CTRL-V is the only option, which it sometimes is, take your time to understand and clarify the code by renaming things.
The vanilla code is obfuscated, naturally it takes time and effort to understand it.
You might discover parts you can omit or change, that you would otherwise not find.
Your future self will thank you for doing it now instead of later.

### Example: Creating a fence block
You should be looking for an opportunity to reuse the code for the vanilla fence blocks.
Your primary goal should be to simply use the vanilla `BlockFence` class without copying anything.
The class is there, it's only a matter of constructing a new instance of it or extending it to add whatever custom things you need.

# Avoid the "Base class" trap
A sizeable part of a mod is creating and registering objects like Blocks and Items.
It is desireable to generalize the logic of setting registry names, translation keys and registering objects and models.

Minecraft itself relies on class inheritance for a lot of its logic, including Blocks and Items.
Unfortunately, these classes are missing some things modders would consider convenient.
The most common thing modders tend to tack on are setting registry names and translation keys in the constructor, as well as adding a method to register an item model.

As it turns out, inheritance is not a good tool to work around the limitations of inheritance.
The way this is often done requires a lot of copy-pasting which violates the "Don't Repeat Yourself" principle or re-uses the vanilla code the wrong way.
However, the use of a reusable utility method can go a long way:
```java
static Block withName(Block block, String name) {
    return block
        .setRegistryName(MODID, name)
        .setTranslationKey(MODID + "." + name);
}
```

With this approach you can set the registry name and translation key using any Block/Item class without extending the class.
Registry name logic and translation key logic can be removed from your classes entirely.
This should be considered a major win if you favor the DRY principle.

```java
registry.registerAll(
    withName(new Block(Material.GRASS), "example_block"),
    withName(new BlockFence(Material.CLOTH, MapColor.RED), "example_fence"),
    withName(new MyBlock(), "example_myblock")
);
```

# Know your ItemBlocks
Not all Blocks should have ItemBlocks, and some ItemBlocks are special.
Example vanilla Blocks without ItemBlocks are fluids, doors, portals, powered versions of various redstone components and piston heads.
Some ItemBlocks are special. In Minecraft 1.12 there are 11 subclasses of ItemBlock, such as ItemSlab and ItemMultiTexture.

Be aware of how you register your ItemBlocks.
Prefer handling ItemBlock registration explicitly instead of blanket registering ItemBlocks for every Block you have.

# Never trust the client
"Never trust the client" is a principle of software security in a client-server architecture.
It means that any information coming from the client to the server should be treated as though you do not trust it, to ensure the client can only do what it is supposed to.
There really is no excuse to ignore software security.
Disobeying this principle can cause unexpected behavior and create attack vectors on servers for people with malicious intent.

A practical implication of this principle is that a BlockPos coming from a client could be _absolutely any BlockPos_, which includes those belonging to chunks that are not even loaded.
Generally, you must check the BlockPos with `World.isBlockLoaded(BlockPos)` before calling `getBlockState`, `setBlockState`, `getTileEntity` and similar methods with the BlockPos.
Otherwise, you risk allowing arbitrary chunk loading and generation which can bring a server to a halt when exploited.

# Synchronize on a need-to-know basis
When dealing with tile entities, you have to make decisions on how to synchronize the state of the tile entity.
Synchronization has a networking overhead, so you should optimize for as little network traffic as possible.
The simple way to do this is synchronizing on a need-to-know basis.

Send only the information the client needs when the client needs it.

### Example: Making a furnace-like device
The only situation where the client needs to know about the smelting progress and the inventory is when the player is viewing the GUI.
Vanilla already has an interface that should be used for this.
Container is the server-side equivalent of a GUI which handles synchronization of data in GUIs.

The correct synchronization implementation in this case is using the Container.
Triggering tile entity updates for the smelting progress and inventory would be entirely unnecessary as the client would have no use for the information until the player opens the GUI.

### Example: Making a GUI-less device containing energy
Assuming the client has no way to see or interact with the energy level, there is no need for the client to know the energy level at all.

The correct synchronization implementation in this case would be no synchronization at all!
Triggering tile entity updates for the energy level would be entirely unnecessary as the client would have no use for the information at all.

# Use scheduled tasks for message handling
Minecraft's use of Netty means that networking is asynchronous and happens on another thread.
This adds the challenges of dealing with concurrency, something you would rather not have to deal with.
One of the worst issues is causing ConcurrentModificationExceptions, which are hard to debug.

The solution to these troubles is to always use the scheduled tasks mechanism provided and used by vanilla.
The `IThreadListener` interface is implemented by classes like WorldServer, Minecraft and MinecraftServer and is used to schedule a task for the next tick to be run synchronously with its respective thread.

### Example: Handling a server bound message
```java
IMessage onMessage(MyMessage message, MessageContext ctx) {
    EntityPlayerMP player = ctx.getServerHandler().player;
    WorldServer world = player.getServerWorld();
    world.addScheduledTask(() -> {
        // handle message synchronously with server thread here
    })
}
```

### Example: Handling a client bound message
```java
IMessage onMessage(MyMessage message, MessageContext ctx) {
    Minecraft.getMinecraft().addScheduledTask(() -> {
        // handle message synchronously with client thread here
    })
}
```

[Refer to the Forge documentation for more details](https://mcforge.readthedocs.io/en/latest/networking/simpleimpl/).

# Use non-blocking I/O
In the context of a game, especially a loading heavy, barely threaded one like Minecraft, time is a precious resource.
You should therefore design your I/O to be non-blocking, as blocking I/O operations can take a lot of time during loading and cause huge lag spikes in-game.

In practice, this means if you want to access a resource on the Internet like version data or a list of users, you should do so on another thread to avoid blocking everything else. If you are using a resource on the Internet to nag users about new versions, consider using the [Forge Update Checker](https://mcforge.readthedocs.io/en/latest/gettingstarted/autoupdate/).

# Localize your strings (in the right locale)
Minecraft is available in many languages, and your mod can be too.
You should use the I18n (internationalization) system provided by Minecraft, even if you do not intend to localize in any other language just yet.
It provides a simple and correct way to deal with user facing strings that allows you or someone else to localize in other languages in the future.

This means you should avoid writing hardcoded user facing strings. Put them all in your language file, and localize with appropriate methods.
A consequence is that you should almost always prefer using TextComponentTranslation over TextComponentString.

Also be aware of _where_ you are localizing.
To localize in the user's locale, the client has to localize, not the server!
Always send language neutral messages to the client using methods like TextComponentTranslation to respect the player's language settings.

[Refer to the Forge documentation for more details](https://mcforge.readthedocs.io/en/latest/concepts/internationalization/).

# Namespace your translation keys
Unlike registry names, translation keys are not namespaced, which means it is easy to create conflicts.
In the event of a conflict, one will override the other, and you get [interesting situations like this one](https://github.com/aidancbrady/Mekanism/issues/5111).
To avoid conflicts, put your mod ID somewhere in every translation key you define:

```properties
tile.modid.my_block.name=My Block
```

To do this with Blocks and Items you must do the following:

```java
block.setTranslationKey(MODID + "." + name);
```

# Identify players by UUID, not username
Player UUIDs are unique and permanent.
Usernames are unique but not permanent.
A player can change their username, but not their UUID.
This means if you need to permanently refer to a player or use something as a key, use the UUID.