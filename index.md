Vita SDK is an open source toolchain for PlayStation&reg; Vita development. This allows you to create applications, games, and more (homebrew) to run on hacked Vita consoles. You can find more information on the hack [here](https://henkaku.xyz/).

# Getting Started

## Prerequisite

### Linux

Install the following (adapt the command for your system): `apt-get install make git-core cmake`

### OSX

1. Install either [brew](http://brew.sh) or [MacPorts](https://www.macports.org)
2. Run either `brew install wget cmake` or `port install wget cmake`.

### Windows

If you have [Bash on Ubuntu on Windows](https://msdn.microsoft.com/en-us/commandline/wsl/install_guide), then the recommended way is to follow the Linux directions above. Otherwise, continue reading.

1. Install [msys2](http://repo.msys2.org/distrib/msys2-x86_64-latest.exe)
2. Run mingw64 shell (as administrator) and install the dependencies: 

`pacman -Su make git cmake tar mingw64/mingw-w64-x86_64-libwinpthread-git`

and

`pacman -S mingw-w64-x86_64-python3-bsddb3 mingw-w64-x86_64-gexiv2 mingw-w64-x86_64-ghostscript mingw-w64-x86_64-python3-cairo mingw-w64-x86_64-python3-gobject mingw-w64-x86_64-python3-icu mingw-w64-x86_64-iso-codes mingw-w64-x86_64-hunspell mingw-w64-x86_64-hunspell-en mingw-w64-x86_64-enchant`
note: Always run msys2 as administrator!

## Installing

Set the environment variable `VITASDK` to be the install destination for Vita SDK. We recommend using `/usr/local/vitasdk`. We also recommend making the change to your `.bashrc` or equivalent (`.bash_profile` on OSX) since using the toolchain requires `VITASDK` to be set.

```
export VITASDK=/usr/local/vitasdk
export PATH=$VITASDK/bin:$PATH # add vitasdk tool to $PATH
```

Download the [host package manager](https://github.com/vitasdk/vdpm) and install to `$VITASDK`

```
git clone https://github.com/vitasdk/vdpm
cd vdpm
./bootstrap-vitasdk.sh
./install-all.sh
```

This will install the toolchain and the most common libraries (including zlib, freetype, [libvita2d](https://github.com/xerpi/libvita2d), [taihen](https://tai.henkaku.xyz/) and more).

## Updating

With vitasdk installed and in your `PATH`, just run `vitasdk-update` to get the latest toolchain build.

## Removing

To remove all traces of Vita SDK, just delete your `$VITASDK` directory. You can then reverse the steps in installing the prerequisite.

# Platform

## Writing Code

The [APIs](https://docs.vitasdk.org/) are gathered from reversing the Vita's firmware. It includes everything you can do with the system. It is recommended that you start by cloning the [samples](https://github.com/vitasdk/samples) and by reading through them and understanding them. The environment is POSIX but the true power comes from the Sony specific [APIs](https://docs.vitasdk.org/).

### Plugins and Extensions

It is highly recommended that you are familiar with writing homebrews before attempting to write a plugin, which is significantly more difficult to do. Advanced developers can check out [taiHEN](https://tai.henkaku.xyz/) for a substrate that allows you to hook system functions.

## Building

Building is done with CMake and you should familiarize yourself with the [hello world CMake file](https://github.com/vitasdk/samples/blob/master/hello_world/CMakeLists.txt) to better understand how it works. SELF is the executable format that the Vita runs and the included CMake macro `vita_create_self` will build it for you. VPK is the homebrew package format and is an archive of the SELF along with data files and LiveArea and is done by the CMake macro `vita_create_vpk`. VPK is what is installed to the Vita as a bubble.

## Running

To run homebrew, you need a hacked Vita or PS TV. Currently only known hacks are [HENkaku](https://henkaku.xyz/) which requires firmware 3.60, [h-encore](https://github.com/soarqin/finalhe) which requires firmware 3.65 - 3.68, and [Trinity](https://github.com/TheOfficialFloW/Trinity) which requires firmware 3.69 - 3.70 (if you are on firmware above 3.61 it's recommended to downgrade to 3.60 using modoru) Once you installed HENkaku, h-encore or Trinity, open up molecularShell/VitaShell and copy your built VPK to `ux0:data` through FTP. You can then install it as a bubble.

## Debugging

Currently, there is minimal debugging support. If your app crashes, a core dump will be generated in `ux0:data`. You can then [parse the core dump](https://github.com/xyzz/vita-parse-core) to figure out what caused the crash. If you would like to help contribute to the Vita SDK project, we would love to have a debugger for the system.

## Porting libraries

vitasdk ships with a flexible `makepkg`-based build system for building packages that can later be installed into the SDK. To get started, clone [vitasdk/packages](https://github.com/vitasdk/packages), `cd` into a directory with a `VITABUILD` file and run `vita-makepkg`. Once the package is built, you can install it with `vdpm ./name-version-arm.tar.xz`.

If you want to write your own `VITABUILD` file, here are a few examples that you can follow: [a library using autotools-style build](https://github.com/vitasdk/packages/blob/master/expat/VITABUILD), [a library using CMake](https://github.com/vitasdk/packages/blob/master/glm/VITABUILD), and [a library where a Vita-specific patch is required](https://github.com/vitasdk/packages/blob/master/libsndfile/VITABUILD).

# Community

Thanks to the [Vita SDK team](https://github.com/orgs/vitasdk/people) for creating everything! The logo was designed by [@Acemad_](https://twitter.com/Acemad_).

You can find most of us in the [#vitasdk room in FreeNode IRC](http://webchat.freenode.net/?channels=%23vitasdk). If you have any questions or need any help, don't hesitate to ask! We also have a [forums](https://forums.vitasdk.org/) for discussions and a [wiki](https://wiki.henkaku.xyz/) for details on reversing engineering the Vita.
