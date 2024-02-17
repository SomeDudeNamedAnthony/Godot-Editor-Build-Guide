
# Godot Editor Build Guide

## Building Godot 4.1.x Editor

This guide will show you how to build Godot 4.1.x Editor for Windows in a Docker container.  
The guide will only teach you how to build the editor **not** the export templates. For that  
please view my other guide.  

We'll be using [RedHat's Fedora Linux](https://fedoraproject.org) version *40*.  
The guide could also apply to other Linux containers and other versions of Fedora.  

### Prerequisites

First we have to update the`dnf` mirror before being able to install anything.  
**Run this command to update all the mirrors:**  

```sh
dnf update
```

Now we can get all of the required tools for building Godot.  

```sh
dnf install \
python3 \
python3-pip \
wget \
xz \
mingw64-gcc-c++ \
mingw64-winpthreads-static \
mingw32-gcc-c++ \
mingw32-winpthreads-static
```

```txt
Note: SCons will be installed in a Python Virtual Environment.
```

* [SCons](https://scons.org) is required for building Godot.  
* [Python 3](https://www.python.org) is required for SCons.  
* Python 3 is required to install SCons.  
* [Wget](https://www.gnu.org/software/wget) is used to download Godot. (*Curl or another program can be used*)  
* The [MinGW](https://www.mingw-w64.org) toolchain is required to cross-compile to Windows.  
* [XZ Utilities](https://xz.tukaani.org/xz-utils) is required to extract the source code.  

### Configuration

Now that we got all of the tools we can get started.  

We'll first use a separate directory for building, in this case `/tmp`.  
Just enter it with this command.  

```sh
cd /tmp
```
*If you don't have `/tmp`, then you have bigger problems.*  

*Skip this step if you already have Godot's source code.*  
First we have to download Godot so run this command:  
> Note: **You will need an internet connection to download Godot.**  

```sh
wget "https://github.com/godotengine/godot/releases/download/4.1.3-stable/godot-4.1.3-stable.tar.xz"
```

Now we can extract the archive with these to commands:  

```sh
tar xf './godot-4.1.3-stable.tar.xz' && cd './godot-4.1.3-stable'
```

Now that we're in the source folder, we can create a `venv` to install SCons.  
Use this command to create and use a `venv`:  

```sh
python3 -m venv './.venv' && source ./.venv/bin/activate
```

And use this command to install SCons:  

```sh
pip install scons
```
*Here we don't need `python3 -m pip` as the `venv` aliases it for us.*  

### Building

Now run these commands to build the editor:
> Use the command for your platform or build all 3.

```sh
scons platform=windows target=editor optimize=size lto=full use_mingw=yes production=yes arch=x86_64 #64-bit
scons platform=windows target=editor optimize=size lto=full use_mingw=yes production=yes arch=x86_32 #32-bit
```
