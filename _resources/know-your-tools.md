---
title: "Know your tools"
---
This resource explains MCP and SRG environments, as well as the tools that support it, Gradle and ForgeGradle.
It should be clear what the tools are and what they are not, and how they are related.

# MCP, environments and naming
The naming of classes, fields and methods are easily taken for granted. It is not as trivial as it seems on the surface, and sometimes you have to see through the magic.

Releases of Minecraft Java Edition are obfuscated jar files, which means they are compiled binaries stripped of any meaningful naming information. Usually the process of reverse engineering a compiled program is complicated, but Java programs like Minecraft are rather simple to decompile. Still, the obfuscation is stripping away a lot of useful information, so one might wonder how it is possible to develop against it in the first place.

MCP (Mod Coder Pack) [hosts a massive community project](https://github.com/ModCoderPack/MCPBot-Issues) to assign meaningful names for Minecraft classes.
Thanks to MCP, it is possible to make sense of the Minecraft code and develop against it.
MCP routinely releases mappings.
A mapping provides javadocs and names for fields, methods, parameters, and local variables.
Mappings are not perfect and often change as more accurate names are found, but they are absolutely essential.

In a development environment the game is running with MCP names to allow code be meaningful like `Minecraft.getMinecraft()`.
This is really nice, but it is worth knowing that these names only exist in your development environment.
Once you leave the development environment, the meaningful MCP names are gone and replaced by SRG names.
SRG names are names like `func_192989_b` and `field_71432_P` for methods and fields respectively.
These names are canonical. MCP names can change while SRG names can not.
There are multiple ways to get SRG names from MCP names and vice versa. [One option is this online tool](http://mcp.thiakil.com/).

| MCP (stable_39) | SRG             | "Notch" (1.12.2) |
| --------------  | --------------- | ---------------- |
| `getMinecraft`  | `func_192989_b` | `M`              |

There is a concept of so-called "Notch" names, but these names are virtually transparent to modders.
Notch names are assigned seemingly randomly by RetroGuard, the obfuscator used to make Minecraft Java Edition releases.
Forge replaces "Notch" names with SRG names so that mods will run in a SRG environment.
This trick allows mods to be compatible with multiple Minecraft versions.

# Gradle and ForgeGradle
[Gradle](https://gradle.org/) is a build tool that is commonly used to manage Java projects, with IDE integration and a rich API to build plugins.
Gradle has uses far beyond modding and has plenty of documentation.
The Gradle wrapper, `gradlew`, is an utility that allows developers to use Gradle without installing it.
It is commonly included with Gradle projects for ease of use, and is included in the Mod Development Kit.

`build.gradle` is the buildscript which defines your project.
Your IDE can import the Gradle project to create a IDE project of its own.
Because the IDE project is a copy of the Gradle project, you need to be careful when applying changes to the project.
Apply changes to the buildscript, not directly to your IDE project, then refresh the project in your IDE to apply the changes.

ForgeGradle is a Gradle plugin that allows developing mods.
ForgeGradle does a lot of heavy lifting to allow mods to interface with Minecraft and Forge with respect to MCP and SRG environments.
First and foremost, it provides an MCP environment containing Minecraft and Forge by decompiling, patching, then recompiling Minecraft.
Secondly, it allows you to build mods so that they work in SRG environments.