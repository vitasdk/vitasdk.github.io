Vita SDK is an open source toolchain for PlayStation&reg; Vita development. This allows you to create applications, games, and more (homebrew) to run on hacked Vita consoles. You can find more information on the hack [here](https://henkaku.xyz/).

# Getting Started

## Prerequisite

### Linux

Install the following (adapt the command for your system): `apt-get install make git-core cmake python`

### OSX

1. Install either [brew](http://brew.sh) or [MacPorts](https://www.macports.org)
2. Run either `brew install wget cmake` or `port install wget cmake`.

### Windows

1. Install [WSL2](https://msdn.microsoft.com/en-us/commandline/wsl/install_guide)
2. Follow the Linux directions above.

A native Windows (mingw) toolchain is also published with every release, installable with `bootstrap-vitasdk.ps1` from [vdpm](https://github.com/vitasdk/vdpm) — but WSL2 is the recommended environment.

#### Option

By installing [Visual Studio Code](https://code.visualstudio.com/), you can flexibly develop in cooperation with WSL2.

To link Visual Studio Code and WSL2, select [Connect to WSL] from the remote button.

![vs_code_remote.png](https://raw.githubusercontent.com/vitasdk/vitasdk.github.io/master/res/vs_code_remote.png)


Also if you have error line missing headers for vitasdk in Visual Studio Code, Create a directory named `.vscode` in the same directory as CMakeLists.txt and [download this file](https://raw.githubusercontent.com/vitasdk/vitasdk.github.io/master/res/c_cpp_properties.json) there.

## Installing

Set the environment variable `VITASDK` to be the install destination for Vita SDK. We recommend using `/usr/local/vitasdk`. We also recommend making the change to your `.bashrc` or equivalent (`.bash_profile` on OSX) since using the toolchain requires `VITASDK` to be set.

```
export VITASDK=/usr/local/vitasdk
export PATH=$VITASDK/bin:$PATH # add vitasdk tool to $PATH
```

Download the [host package manager](https://github.com/vitasdk/vdpm) and bootstrap VitaSDK. The bootstrap installs the newest supported release — currently **2026.08** — and selects its channel:

```bash
git clone https://github.com/vitasdk/vdpm
cd vdpm
./bootstrap-vitasdk.sh
```

Check what you got:

```bash
vdpm status
```

To install packages and libraries (such as zlib, SDL2, libvita2d, taihen) — dependencies are resolved automatically:

```bash
vdpm install zlib sdl2 libvita2d taihen
```

## Updating

There are two update commands, and the difference matters:

```bash
vdpm upgrade          # package fixes within your release — safe, never changes your toolchain
vdpm refresh 2026.08  # move to a release, or re-sync the one you are on
```

`vdpm upgrade` picks up package fixes published for the release you are on. It will never move you to a new toolchain behind your back: that only happens when you explicitly `refresh` to a newer release after one is announced.

## Releases and channels

VitaSDK ships as versioned, signed releases through package channels. List them with `vdpm channels`:

- **2026.08** (supported) — what most homebrew is built against. Newlib 4.1.
- **nightly** (development) — rebuilt continuously; expect it to move under you.

Everything a channel serves is signed, and `vdpm` verifies it on every refresh and install. `vdpm status` prints the exact release and sequence you are on — include it in bug reports.

Already have an older vitasdk install? See the [migration guide](migration) — nothing breaks if you stay, and moving takes minutes.

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

To run homebrew, you need a hacked Vita or PS TV. Currently only known hacks are [HENkaku](https://henkaku.xyz/) which requires firmware 3.60, [h-encore](https://github.com/soarqin/finalhe) which requires firmware 3.65 - 3.68, and [Trinity](https://github.com/TheOfficialFloW/Trinity) which requires firmware 3.69 - 3.70 (after hacking your device if you are on firmware above 3.61 it's recommended to downgrade to 3.60 using modoru) Once you installed HENkaku, h-encore or Trinity, open up molecularShell/VitaShell and copy your built VPK to `ux0:data` through FTP. You can then install it as a bubble.

## Debugging

Currently, there is minimal debugging support. If your app crashes, a core dump will be generated in `ux0:data`. You can then [parse the core dump](https://github.com/xyzz/vita-parse-core) to figure out what caused the crash. If you would like to help contribute to the Vita SDK project, we would love to have a debugger for the system.

## Porting libraries

vitasdk ships with a flexible `makepkg`-based build system for building packages that can later be installed into the SDK. To get started, clone [vitasdk/packages](https://github.com/vitasdk/packages), `cd` into a directory with a `VITABUILD` file and run `vita-makepkg`. Once the package is built, you can install it with `vdpm ./name-version-arm.tar.xz`.

If you want to write your own `VITABUILD` file, here are a few examples that you can follow: [a library using autotools-style build](https://github.com/vitasdk/packages/blob/master/expat/VITABUILD), [a library using CMake](https://github.com/vitasdk/packages/blob/master/glm/VITABUILD), and [a library where a Vita-specific patch is required](https://github.com/vitasdk/packages/blob/master/libsndfile/VITABUILD).

# Community

Thanks to the [Vita SDK team](https://github.com/orgs/vitasdk/people) for creating everything! The logo was designed by [@Acemad_](https://twitter.com/Acemad_).

You can find most of us on [Discord](https://discord.gg/AQc7KWG) or Matrix (links at the top of this page). If you have any questions or need any help, don't hesitate to ask! We also have a [forums](https://forums.vitasdk.org/) for discussions and a [wiki](https://wiki.henkaku.xyz/) for details on reversing engineering the Vita.
