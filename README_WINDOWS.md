# Building MyPaint on Windows

## Setup.exe and standalone

The `windows` subdirectory contains scripting for making a Win32
installer from git. This may be all you need to build MyPaint from
source on Windows. See [windows/README.md][1] for detailed instructions.

The remainder of this document describes a manual process intended for
developers and testers.

## Manual building and testing

This document describes building a native Win32 MyPaint using MSYS2's
MinGW-w64. The resultant script and its extension DLL must be run with
MSYS2's own Python build for the target system (/mingw32 or /mingw64).
Running it depends on external libs like GTK3 packaged by the MSYS2
team, all of which are also installed into the target system.

The only supported method for running MyPaint with these instructions is
from the command line.

This doc DOES NOT COVER building MyPaint into an installer bundle See
[windows/README.md][1] if you want to do that. This document covers
only the bare essentials needed to get MyPaint running for debugging
purposes.

### Get and update MSYS2

MSYS2 is a free toolchain,
build environment,
and binary library distribution for Windows.
It can create native PE32 binaries for Windows
using the MinGW-w64 compiler.

To install MSYS2, follow the instructions at

* https://msys2.github.io/

One installed, update MSYS2 as instructed.
Use the **MSYS** environment for this:
it's launched using the “MSYS2 Shell” shortcut in the start menu
for this, or `C:\msys64\msys2_shell.bat`.

    pacman --needed -Sy bash pacman msys2-runtime pacman-mirrors
    ; then close and reopen the shell
    pacman -Syu
    ; restart shell again

#### Build environments

The build commands below must be run in the environment
which gives you the correct path for the
target architecture and prefix you will be compiling for.
These are:

* **MINGW32**:
  - creates Win32 executables in `/mingw32` (`C:\msys64\mingw32`)
  - start menu: “MinGW-w64 Win32 Shell”
  - launcher script: `C:\msys64\mingw32_shell.bat`
* **MINGW64**:
  - creates Win64 executables in `/mingw64` (`C:\msys64\mingw64`)
  - start menu: “MinGW-w64 Win64 Shell”
  - launcher script: `C:\msys64\mingw64_shell.bat`

The text in bold refers to what you see in the prompt.
I'll use those names to refer to the environments below.
The only real difference between the three environments is
the `PATH` environment variable.

### Get a development copy of MyPaint

You'll need the MSYS2 git and the standard developer tools.
In any MSYS2 environment, issue

    pacman -S base-devel git

Once that's done, clone MyPaint,
and make sure all its submodules are present in the source tree.
These commands will work in any MSYS2 environment.

    cd /usr/src
    git clone https://github.com/mypaint/mypaint.git
    cd mypaint
    git submodule update --init

### Install MyPaint's dependencies

All of MyPaint's dependencies are available from the MSYS2 repositories.
Thanks to everyone maintaining [MINGW-packages][2] for giving us
a great open platform to build against!

To install MyPaint's dependencies,
start MSYS2's **MINGW32** or **MINGW64** shell.
There's a script for installing the dependency packages
in the `windows/` folder of the source tree:

    cd /usr/src/mypaint
    windows/install-msys2-deps.sh

### Build and test MyPaint

Start by fetching and building MyPaint.
You need to do this from the **MINGW32** or **MINGW64** environment
you fetched the dependencies for.

    cd /usr/src/mypaint
    scons

The "scons" used here is actually the **MSYS** environment's scons.
We have several nasty hacks in our SCons scripting to make this work,
but this combination is what the official builds use.

Hopefully after this, you will be able to run MyPaint
from the location you pulled it down to:

    cd /usr/src/mypaint
    MYPAINT_DEBUG=1 ./mypaint -c /tmp/cfgtmp1

MyPaint may be quite a bit more buggy on the Windows platform
than on the Linux platform, be warned.

## Known Problems

* **No pressure support / glitches with tablet drivers.**
  These should be reported to the GDK maintainers.
  See <https://bugzilla.gnome.org/show_bug.cgi?id=743330>
  for an example of how to do this effectively.
  Discussing your issue on [IRC](irc://irc.gnome.org/%23gtk%2B)
  after raising it in the tracker is often very fruitful too.

* **Bugs. Lots of them.**
  The port to Windows has historically received the least love of all
  MyPaint ports, but this document is intended to help address that.
  The number is diminishing, and MyPaint runs on native Windows
  reasonably well now.
  We really need actively testing users to improve support further.
  Please report problems as described in [CONTRIBUTING.md](CONTRIBUTING.md).

[1]: ./windows/README.md
[2]: https://github.com/Alexpux/MINGW-packages
