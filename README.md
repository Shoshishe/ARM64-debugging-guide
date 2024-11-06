# Prerequisites 

First of all to debug arm code on X86_64 machine you have to ***install a cross-compiler***, which could compile arm  
assembly code into native code for ARM platforms, so that it could be used later with emulators or VMs to run it. 

It depends on the exact architecture of ARM, and the OS used when compiling, but more often than not you would prefer to use  
Linux because of the GCC family of libs and tools. For instance on Arch Linux, installing armv8 compiler using pacman:

```
sudo pacman -S aarch64-linux-gnu-gcc
```

For compiling source code into an executable such command may be used:

```
aarch64-linux-gnu-gcc -Wa,-mcpu=cortex-a57 -ggdb3 -o test -static test.s
```

-Wa passes the commands list to the assembler, -mcpu flag means the desired architecture with its features 
(here is used armv8a processor), -ggdb3 flag for generating necessary debugging info, -o for generating an object file called test,
and -static prevents linking with shared libraries, so that an executable could be run without all of runtimes installed here

For later work there is only one ***really*** necessary thing - an emulator of any kind. You can simply not debug code if you are really  
self-confident. The most basic and simpliest choice for an emulator is QEMU, since it requires just installing some packages

```
sudo pacman -S qemu-base
```

# Emulation and debugging 
We don't really have to emulate the full system, just invoking the source file compiled for different architecture
It is done by using the qemu-*architecture name* commands. In this repo we use a command like this:
```
qemu-aarch64 -L /usr/aarch64-linux-gnu -g 8080 ./test
```
-L flag tells the compiler path to the elf interpreter (elf is basically file format, that debuggers natively understand)
-g *port* launches the gdbserver using the given port, and ./test is the already compiled executable

To debug, you have to manually install either gdb-multiarch package and reference it on instances where I specified GDB, or,
which is more preferrable, delete current version of GDB and compile GDB 15.2+ from source ([Link to tar package of 15.2 gdb](https://ftp.gnu.org/gnu/gdb/gdb-15.2.tar.xz)),
to enable multiarchitecture support by default.
For debugging it is necessary to attach gdb to the passed port number to use it, since the source code can't be run
natively. You can invoke it manually, hover, it can be quite cubersome to work with TUI, instead of the usual VScode.

# VScode integration

Basically, just writing makefile, to invoke the compiler, and writing launch and task .json. You can probably do it
without makefile alltogether, allthough, that serves close to no benefits at all. The example of such files with the
commentaries is in the vscode directory.
