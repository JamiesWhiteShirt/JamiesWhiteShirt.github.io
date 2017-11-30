---
title: "Troubleshooting"
series: "The art of problem solving"
---
Troubleshooting is about diagnosing and fixing an issue with something that doesn't work the way it should.
This resource describes a general approach to troubleshooting.

The first part of troubleshooting is **classifying** and **identifying** your problem.

### A note on troubleshooting with tutorials
If you are following a tutorial, ensure you are following the steps correctly and not deviating too much.
You might have forgotten a step.
A deviation from the steps described in the tutorial might also be the cause of your issue.
Be careful with this when you are unfamiliar with the subject. You can follow the tutorial first, then make your own changes later.

## Classifying the problem
The form of the actual solution and how you will find it highly depends on the problem.
You need to be able to **classify** the problem first.
Is it an error? Is it a problem with your code syntax? Is it a problem with a model?
Maybe you are trying to find out how to do a certain thing.

### My code doesn't compile!
You have a Java syntax error! It means the Java compiler doesn't understand your intention. This is the most basic of errors.
Copy the compilation error into the search engine of your choice, and you will most likely find a link to a popular Stack Overflow post on the subject with good answers.

Attempts to ask for help with these errors usually fall short: You will be told to learn Java.
If you want to learn some Java concepts, now is a good time. Try to understand _why_ your code doesn't compile, and how the solution you find resolves it.
The things you learn along the way might be useful in the future!

### My block/item model doesn't work!
Usually the cause of the error can be found in the console. Look for stack traces or error messages among the many lines.
If you are clever, you might be able to determine how to fix your problem based on what the console tells you.
If not, you could consult [greyminecraftcoder's troubleshooting guide](http://greyminecraftcoder.blogspot.no/2015/03/troubleshooting-block-and-item-rendering.html).

Asking for help with this will often take more time than resolving the issue yourself.
This is because you are the one with easy access to the mod's JSON files and textures.
You will find that communicating your files and folder structure is necessary and difficult.

### The game crashes!
This class of problem is more difficult to troubleshoot because crashes can be caused by a multitude of things.
Resolving these problems on your own requires a basic understanding of programming. You must first determine the cause of the issue.

Inspect the type and the stack trace of the exception that causes the crash:
- **The type of exception** may indicate what kind of problem it is.
  Try pasting the type and the message in the search engine of your choice and see what shows up.
  For instance, if your exception is a `java.lang.NullPointerException` you have most likely accessed something that has not been initialized.
  Maybe you did something in the wrong order?
- **The stack trace** of the exception will likely refer to lines in your code. Inspect these lines, and see if something is off.

If you cannot determine the cause of the crash from the exception, try **isolating the problem**.
Disable parts of your code, narrow it down to a specific part, and work from there.

It is generally OK to ask for help with this class of problem.
As with any other problem, you should still do your research beforehand.
Make sure to specify what you were trying to do that caused the crash.
You will likely be asked to share your code. Don't be afraid of doing this, because nobody wants to steal your code.

### My thing doesn't work, or doesn't work the way I want it to!
This class of problem is similar to crash problems.
They can be caused by a multitude of things, and resolving these problems on your own requires a basic understanding of programming.

Here are some common errors:
- Usually, if what you are doing involves **events**, you may have done something incorrectly to register your event handler.
  Consult the [official MinecraftForge documentation](https://mcforge.readthedocs.io/en/latest/events/intro/) for information on how you might have failed.
- If your item/block doesn't appear ingame you probably forgot to register the item/block.

To attempt to understand _why_ the thing doesn't work, you should try to:
- Use `System.out.println` to print debug information to the console. Use it to verify that your code is called and with the values you expect.
- **Use a debugger**. A debugger is a very powerful and useful tool that you should dare to learn how to use.
  Not only can you inspect the execution of your own code, you can attach to other code to inspect how it interacts with yours.
  It also makes fiddling with `System.out.println` in your code obsolete because you can retrieve more or less the same information.

When searching or asking for help with this kind of problem, **saying it "doesn't work" is never specific enough**.
Your helpers are not clairvoyant. Your query must consist of these three parts:
1. **What you did**: What code did you write? Where and when is it called?
2. **What happened**: What happened that didn't meet your expectations?
3. **What did you expect to happen?**

It is generally OK to ask for help with this class of problem. As with any other problem, you should still do your research beforehand.
You will likely be asked to share your code. Don't be afraid of doing this, because nobody wants to steal your code.
