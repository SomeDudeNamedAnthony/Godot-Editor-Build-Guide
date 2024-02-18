
# Godot Editor Build Guide

## Building Godot 4.2.x Editor

This guide will show you how to build Godot 4.2.x Editor for Windows in a Docker container.  
The guide will only teach you how to build the editor **not** the export templates. For that  
please view my other guide.  

We'll be using [RedHat's Fedora Linux](https://fedoraproject.org) version *36*.  
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

* SCons is required for building Godot.  
* Python 3 is required for SCons.  
* Python 3 is required to install SCons.  
* Wget is used to download Godot. (*Curl or another program can be used*)  
* The MinGW toolchain is required to cross-compile to Windows.  
* XZ Utilities is required to extract the source code.  

### Configuration (Part A)

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
wget "https://github.com/godotengine/godot/releases/download/4.2.1-stable/godot-4.2.1-stable.tar.xz"
```

Now we can extract the archive with these to commands:

```sh
tar xf './godot-4.2.1-stable.tar.xz' && cd './godot-4.2.1-stable'
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

### Configuration (Part B)

Now we all that being done, we can get to the good part.

If you want to build Godot optimized for size then you can use this build profile  
I created.
> Put this in a python file. By default `custom.py` is used if found.

```py
platform = "windows"
target = "editor"
production = "yes"
optimize = "size"
lto = "full"
```

*To write this to a file via a command:*

```sh
cat > './custom.py' << EOF
platform = "windows"
target = "editor"
production = "yes"
optimize = "size"
lto = "full"
EOF
```

And last we can build the editor with this command:

```sh
scons profile=custom.py
```
