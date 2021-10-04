# Requirements

## Hardware requirements.

### CPU

A x86_64 architecture CPU with a minimum of SSE2 support. This includes
Intel Core2 and newer and AMD equivalents. In order to take advantage of AES_NI
optimizations a CPU with AES_NI is required. This includes Intel Westmere
and newer and AMD equivalents. Further optimizations are available on some
algoritms for CPUs with AVX and AVX2, Sandybridge and Haswell respectively,
as well as AVX512, SHA, and VAES avaible on Icelake.

Older CPUs are supported by cpuminer-multi by TPruvot but at reduced
performance.

ARM, Aarch64, Raspberry Pi are not supported.

### Memory

Memory requirements are very dependant on the algorithm. Some algorithms require over 100 MB per thread.
GPU mining will also raise memory requirements. It's not a performance issue, it works smoothly or it
doesn't.

## Software requirements

### Operating system

64 bit Linux OS. Ubuntu and Fedora based distributions, including Mint and
Centos, are known to work and have all dependencies in their repositories.
Others may work but may require more effort. Older versions such as Centos 6
don't work due to missing features.
64 bit Windows OS is supported with mingw_w64 and msys or pre-built binaries.

FreeBSD and other BSDs may work, YMMV.

MacOS, OSx, Android, Haiku and reactOS are not supported.

### Virtualization and Emulation

It is possible to run cpuminer-opt in a virtual machine but some CPU features may be missing emulation.
CygWin has been known to work but has significantly lower performance than running a Windows binary natively.
VMs and emulators are nit recommended.

### Server

A stratum server for shared mode pool mining. Some pools also offer solo mining.

A full node wallet for the coin to be mined using getwork.

GBT is YMMV.

