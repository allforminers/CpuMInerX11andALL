# Building

## Building on Linux

### Building on linux prerequisites:

It is assumed users know how to install packages on their system and
be able to compile standard source packages. This is basic Linux and
beyond the scope of cpuminer-opt. Regardless compiling is trivial if you
follow the instructions.

Make sure you have the basic development packages installed.
Here is a good start:

http://askubuntu.com/questions/457526/how-to-install-cpuminer-in-ubuntu

Install any additional dependencies needed by cpuminer-opt. The list below
are some of the ones that may not be in the default install and need to
be installed manually. There may be others, read the compiler error messages,
they will give a clue as to the missing package.

The following command should install everything you need on Debian based
distributions such as Ubuntu. Fedora and other distributions may have similar
but different package names.

`
$ sudo apt-get install build-essential automake libssl-dev libcurl4-openssl-dev libjansson-dev libgmp-dev zlib1g-dev git
`

SHA support on AMD Ryzen CPUs requires gcc version 5 or higher and
openssl 1.1.0e or higher. Add one of the following to CFLAGS for SHA
support depending on your CPU and compiler version:

"-march=native" is usually the best choice and is used by build.sh

"-march=znver1" for Ryzen 1000 & 2000 series, znver2 for 3000.

"-msha"  Add SHA to other tuning options

Additional instructions for static compilalation can be found here:
https://lxadm.com/Static_compilation_of_cpuminer
Static builds should only considered in a homogeneous HW and SW environment.
Local builds will always have the best performance and compatibility.

### Download cpuminer-opt

Download the source code for the latest release from the official repository.

https://github.com/JayDDee/cpuminer-opt/releases

Extract the source code.

`$ tar xvzf cpuminer-opt-x.y.z.tar.gz`


Alternatively it can be cloned from git.

`$ git clone https://github.com/JayDDee/cpuminer-opt.git`

###  Build cpuminer-opt

It is recomended to Build with default options, this will usuallly
produce the best results.

`$ ./build.sh`

or


`$ ./autogen.sh`

`$ CFLAGS="-O3 -march=native -Wall" ./configure --with-curl`

`$ make -j n`


n is the number of threads.

### Start mining.


`$ ./cpuminer -a algo -o url -u username -p password`


## Building for Windows

Windows compilation using Visual Studio is not supported. Mingw64 is
used on a Linux system (bare metal or virtual machine) to cross-compile
cpuminer-opt executable binaries for Windows.

These instructions were written for Debian and Ubuntu compatible distributions
but should work on other major distributions as well. However some of the
package names or file paths may be different.

It is assumed a Linux system is already available and running. And the user
has enough Linux knowledge to find and install packages and follow these
instructions.

First it is a good idea to create new user specifically for cross compiling.
It keeps all mingw stuff contained and isolated from the rest of the system.

Step by step...

### Install packages 

Install necessary packages from the distribution's repositories.

Refer to Linux compile instructions and install required packages.

Additionally, install mingw-w64.

`sudo apt-get install mingw-w64 libz-mingw-w64-dev`


### Create a local library

Create a directory for libraries that that need to be compiled in the next stept step.
The Suggested location is $HOME/usr/lib/

`$ mkdir $HOME/usr/lib`

### Build libraries

Download and build other packages for mingw that don't have a mingw64 version available in the repositories.

Download the following source code packages from their respective and respected download locations,
copy them to $HOME/usr/lib/ and uncompress them.

openssl: https://github.com/openssl/openssl/releases

curl: https://github.com/curl/curl/releases

gmp: https://gmplib.org/download/gmp/

In most cases the latest version is ok but it's safest to download the same major and minor version
as included in your distribution. The following uses versions from Ubuntu 20.04. Change version numbers
as required.

Run the following commands or follow the supplied instructions.
Do not run "make install" unless you are using /usr/lib, which isn't recommended.

Some instructions insist on running "make check". If make check fails it may still work, YMMV.

You can speed up "make" by using all CPU cores available with "-j n" where n is the number of
CPU threads you want to use.

openssl:

`./Configure mingw64 shared --cross-compile-prefix=x86_64-w64-mingw32-`

`make`

Make may fail with an ld error, just ensure libcrypto-1_1-x64.dll is created.

curl:

`./configure --with-winssl --with-winidn --host=x86_64-w64-mingw32`

`make`

gmp:

`./configure --host=x86_64-w64-mingw32`

`make`

### Tweak the environment.

This step is required everytime you login or the commands can be added to
.bashrc.

Define some local variables to point to local library.


`export LOCAL_LIB="$HOME/usr/lib"`

`export LDFLAGS="-L$LOCAL_LIB/curl/lib/.libs -L$LOCAL_LIB/gmp/.libs -L$LOCAL_LIB/openssl"`

`export CONFIGURE_ARGS="--with-curl=$LOCAL_LIB/curl --with-crypto=$LOCAL_LIB/openssl --host=x86_64-w64-mingw32"`

Adjust for gcc version:

`export GCC_MINGW_LIB="/usr/lib/gcc/x86_64-w64-mingw32/9.3-win32"`


Create a release directory and copy some dll files previously built.
This can be done outside of cpuminer-opt and only needs to be done once.
If the release directory is in cpuminer-opt directory it needs to be
recreated every time a source package is decompressed.

`mkdir release`

`cp /usr/x86_64-w64-mingw32/lib/zlib1.dll release/`

`cp /usr/x86_64-w64-mingw32/lib/libwinpthread-1.dll release/`

`cp $GCC_MINGW_LIB/libstdc++-6.dll release/`

`cp $GCC_MINGW_LIB/libgcc_s_seh-1.dll release/`

`cp $LOCAL_LIB/openssl/libcrypto-1_1-x64.dll release/`

`cp $LOCAL_LIB/curl/lib/.libs/libcurl-4.dll release/`


### Download cpuminer-opt

The following steps need to be done every time a new source package is
opened.

Download the latest source code package of cpumuner-opt to your desired
location. .zip or .tar.gz, your choice.

https://github.com/JayDDee/cpuminer-opt/releases

Decompress and change to the cpuminer-opt directory.


### Compile

Create a link to the locally compiled version of gmp.h

`ln -s $LOCAL_LIB/gmp-version/gmp.h ./gmp.h`

`./autogen.sh`

Configure the compiler for the CPU architecture of the host machine:

`CFLAGS="-O3 -march=native -Wall" ./configure $CONFIGURE_ARGS`

or cross compile for a specific CPU architecture:

`CFLAGS="-O3 -march=znver1 -Wall" ./configure $CONFIGURE_ARGS`

This will compile for AMD Ryzen.

You can compile more generically for a set of specific CPU features
if you know what features you want:

`CFLAGS="-O3 -maes -msse4.2 -Wall" ./configure $CONFIGURE_ARGS`

This will compile for an older CPU that does not have AVX.

You can find several examples in README.txt

If you have a CPU with more than 64 threads and Windows 7 or higher you
can enable the CPU Groups feature by adding the following to CFLAGS:

`-D_WIN32_WINNT=0x0601`

Once you have run configure successfully run the compiler with n CPU threads:

`make -j n`

Copy cpuminer.exe to the release directory, compress and copy the release
directory to a Windows system and run cpuminer.exe from the command line.

Run cpuminer

In a command windows change directories to the unzipped release folder.
to get a list of all options:

cpuminer.exe --help

Command options are specific to where you mine. Refer to the pool's
instructions on how to set them.


