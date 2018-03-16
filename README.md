# sampctl hello world

A simple sampctl tutorial for building a "hello world" library using the
recommended sampctl workflow and tools.

The goal of this tutorial is to make you familiar with the _workflow_ of
building libraries with sampctl. This tutorial uses Visual Studio Code as an
editor, this will work with any editor (even Pawno, if you're oldschool) but VS
Code is my personal favourite and my recommendation - I used Sublime for years
and tried Atom and I prefer VS Code.

## Getting Started

### sampctl

Obviously you need sampctl installed - this is a super simple process and is
described in the official thread or the readme.

[![get-sampctl](https://shields.southcla.ws/badge/get-sampctl-2f2f2f.svg?style=for-the-badge)](http://forum.sa-mp.com/showthread.php?t=651159)

### Visual Studio Code ("vscode")

Get vscode installed - if you're a Sublime user you can follow along in the
terminal but vscode works really well with sampctl and you'll be impressed so
give it a try - you can always uninstall it after if you don't like it!

[![get-vscode](https://shields.southcla.ws/badge/get-vscode-373277.svg?style=for-the-badge)](https://code.visualstudio.com/)

A lot of people think this is Visual Studio - the huge C++/C# development
environment - this is incorrect, VS _Code_ is like the little brother of Visual
Studio and is much more light-weight with faster start-up time and a simpler
interface.

### Pawn Tools for vscode

This is an extension pack I built with help from the community for vscode, it
adds Pawn language syntax highlighting and some additional tools that integrate
with sampctl.

Installing it is as simple as:

![https://i.imgur.com/BqOq1Eq.gif](https://i.imgur.com/BqOq1Eq.gif)

[![get-pawntools](https://shields.southcla.ws/badge/get-pawn--tools-FFA000.svg?style=for-the-badge)](http://forum.sa-mp.com/showthread.php?t=647134)

### Problems?

If you run into issues, just @ me on Discord or ask in this thread!

## Create a Folder for your Project

First you need to create a directory dedicated to your package. This is standard
in many languages. It's common for Pawn includes to only exist in a single .inc
so it might be tempting to keep all your .inc files in a single folder - don't
do this! You'll get lost and disorganised and Pawn Packages (discussed below)
aren't composed of just a single .inc file but a few additional files so it's
necessary to bundle all of these together into a folder.

Once you've done that, open the _folder_ in vscode ("File" > "Open Folder...").
This might be a new concept to you if you're coming from Pawno or other simple
editors. vscode can open individual files _however_ when working on a _project_
where you have an entire folder _dedicated_ to that project, you can open the
entire folder in a single instance of vscode. This makes jumping between files
really easy and with the _integrated terminal_ it becomes trivial to run
commands.

## Create a Package

So now you have all the tools installed and set up a folder you can create a
_Pawn Package_.

[Pawn Packages are described in detail here](https://github.com/Southclaws/sampctl/wiki/Packages) -
essentially, they are just includes but with a bit of extra information to help
sampctl understand how to get them and build them - to help sampctl help _you_!

So how do we do this? Open up the
[vscode _integrated terminal_](https://code.visualstudio.com/docs/editor/integrated-terminal)
with CTRL+` or by clicking "View" > "Integrated Terminal" on the menu at the
top.

This will open your default shell at the bottom of the vscode window - on
Windows this is either CMD or PowerShell, doesn't really matter which you use.

> side note: if you're not using vscode, you can achieve the same results below
> by just using CMD or PowerShell directly. Shift+Right Click inside your
> project's folder in the File Explorer window and clicking "Open Command Prompt
> here" or "Open PowerShell here".

Once you've got a terminal open, simply enter the following command:

```powershell
sampctl package init
```

And follow the on-screen menus to create your package, for example:

![package-init](http://i.imgur.com/kUYsnUW.gif)

This will generate the following files:

* `.gitattributes`: tells _GitHub_ to mark your repo language as "Pawn"
* `.gitignore`: tells _git_ to ignore certain files
* `.vscode`: contains some vscode-specific settings
* `README.md`: documentation template for your package - edit this later
* `dependencies`: this folder contains the includes your package requires
* `pawn.json`: this file is very special!

Feel free to familiarise yourself with these files. They are all necessary to
help certain tools work well. For example, the `.git...` files are for Git and
GitHub, `.vscode` helps your editor provide useful development features and
`pawn.json` is to help sampctl configure your package and speed up your
workflow.

## Write Some Code!

Okay that's all well and good but we still haven't written a single line of code
yet! We'll do that now.

This "Hello World" package isn't a Pawn tutorial, so the code itself will be
very simple.

We're going to write _and test_ a library that simply sends a Hello World
message to the server console and to players that join the server.

Create a file called `helloworld.inc` with the following contents:

```pawn
#include <a_samp>

HelloWorld(playerid) {
    if(IsPlayerConnected(playerid)) {
        printf("Saying hello to %d", playerid);
        SendClientMessage(playerid, 0xFF3200FF, "Hello World!");
    } else {
        print("Hello World sampctl!");
    }
}

public OnPlayerConnect(playerid) {
    HelloWorld(playerid);
    return 1;
}
```

If the code isn't highlighted, click the syntax button at the bottom right of
the vscode window, it probably says "Plain Text" so click it and select "Pawn"
from the drop-down menu.

## Build The Code

Now we have some source code but there's something missing...

You don't typically _compile_ .inc files - they are meant for including. What we
need is an _entry_ file to compile from.

What makes an entry file an entry file? Two properties:

* It must have a `main()` function
* It must be listed in the `pawn.json` file in the `entry` field

Open up your package's `pawn.json` file, you should see something like this:

```json
{
  "user": "Southclaws",
  "repo": "hello-world",
  "entry": "test.pwn",
  "output": "test.amx",
  "dependencies": ["sampctl/samp-stdlib"]
}
```

As you can see, the `sampctl package init` process pre-populated the `entry` and
`output` fields. These fields are simple, `entry` is the file passed to the
compiler and `output` is the name of the resulting .amx file.

Since `test.pwn` is already there (this is the default) go ahead and create a
file named `test.pwn` in your workspace by clicking the "New File" button:

![vscode-newfile](https://i.imgur.com/EOWfo2N.gif)

And in this file, add the following code:

```pawn
#include "helloworld.inc" // include your library

main() {
    // pass an invalid player ID so we get "Hello World sampctl!" in the console
    HelloWorld(-1);
}
```

Now, in the terminal, run `sampctl package build` - this will _magically_
compile the package and output a `test.amx` file to your workspace.

## Build The Code - But In a Cooler Way

Okay so you built some code, nice, but using the terminal isn't really taking
advantage of the neat features of vscode and the Pawn Tools extension.

Without getting into the details of the
[vscode tasks.json](https://code.visualstudio.com/Docs/editor/tasks) file format
and how powerful it is, I'll quickly go over what tasks are.

Inside the `.vscode` directory, you'll find a file named `tasks.json`, this file
declares 4 tasks:

* `build only`: this just builds the package by running
  `sampctl package build` - same as you did in the section above
* `build watcher`: this monitors the package and builds it whenever a file
  changes
* `run tests`: this runs the package
* `run tests watcher`: this monitors the package and builds it whenever a file
  changes

The `watcher` tasks are _really_ useful and the `run` tasks we'll get into in
the next section.

So to really show the power of this, you need to introduce bad code to trigger
an error, in the `main()` function, add an unused variable:

```pawn
main() {
    new unused;
    HelloWorld(-1);
}
```

Then hit CTRL+Shift+B to run the build task, your editor will highlight the line
and also display it in the "Problems" panel at the bottom of the vscode window.

## Build The Code - But In an _Even_ Cooler Way!

So you can build code with keyboard shortcuts, big woop, Pawno can do this...

The real magic here is the `build watcher` task. To run this, hit CTRL+Shift+P
to
[open the _command palette_](https://code.visualstudio.com/docs/getstarted/userinterface#_command-palette).

This is similar to the terminal but it's a special area to write commands to
vscode itself, it allows you to control the editor with just your keyboard,
which can seriously speed up your workflow.

In the command palette, type `Run task`, hit enter or click it and then hit
`build watcher`:

![vscode-runwatcher](https://i.imgur.com/WIzVhjN.gif)

Now remove the `new unused;` you added earlier and save the file with CTRL+S -
watch as sampctl will recompile your code and vscode will update the interface
to remove the notification on the line with the warning.

You can leave the build watcher task running constantly and it will always be
recompiling your code and updating the editor view so you can focus on what's
important: the code.
