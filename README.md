libvivhook
==============

This library hooks into the galcore driver to provide logging functionality in
userspace.

Building
============

Set GCABI according to the version of the Vivante driver used:

    export GCABI=imx6_v5_0_11_p8_41671

It is **extremely important** for the GCABI to match the actual driver in use.
Vivante changes the kernel interface in a non-compatible way even between patch
levels. If these mismatch, all kinds of nonsense can be returned.

Then approximately follow the following steps. `VIVANTE_LIBPATH` must be set to
the path that `libGAL.so` is in:

    git clone https://github.com/etnaviv/galcore_headers.git
    git clone https://github.com/etnaviv/libvivhook.git
    cd libvivhook
    NOCONFIGURE=1 ./autogen.sh
    ./configure --host=${HOST} --prefix=${PREFIX} \
        --with-galcore-include=${PWD}/../galcore_headers/include_${GCABI} \
        --with-galcore-lib=${VIVANTE_LIBPATH}
    make
    make install

This will install both the interposer `viv_interpose.so` and `libviv_hook` to
the provided prefix.

Command stream interception
=============================

A significant part of reverse engineering was done by intercepting command streams while running GL simple demos.

The raw binary structures interchanged with the kernel are written to disk in a `.fdr` file, along
with updates to video memory, to be parsed by the command stream dumper in `etna_viv` and other tools.

This can be done in two ways, both which are part of this project: 

- Explicitly by using the `viv_hook` library, or 
- By using the `viv_interpose.so` interposer library through `LD_PRELOAD`.

The second option is easier so we'll go with that first.

Using the interposer
-----------------------

Using the interposer is easy. Just start the program that you want to intercept libGAL
kernel traffic for with a `LD_PRELOAD` and set the environment variables for configuring it.

For example, the following will write fdr output to `/tmp/fdr.out` and dump it as text
using the command stream dumping tool in `etna_viv`:

    LD_PRELOAD="/path/to/viv_interpose.so" ETNAVIV_FDR="/tmp/fdr.out" ./cube
    /path/to/etna_viv/tools/show_egl2_log.sh /tmp/fdr.out

Various settings can be configured on the command line:

- `ETNAVIV_MAX_FRAMES=n` stop rendering after *n* frames.
- `ETNAVIV_FDR=file` write fdr output to *file*.

It is also possible to override the chip model, features, flags the libGAL
will see from the evironment. These take a decimal or 0xHEX value:

    ETNAVIV_CHIP_MODEL
    ETNAVIV_CHIP_REVISION
    ETNAVIV_FEATURES[0-7]_SET
    ETNAVIV_FEATURES[0-7]_CLEAR
    ETNAVIV_CHIP_FLAGS_SET
    ETNAVIV_CHIP_FLAGS_CLEAR

This allows testing the Vivante drivers with various different environments, to
see what the difference in the produced command stream is.

viv_hook library
-----------------

`viv_hook` is a library to intercept and log the traffic between `libGAL` (the Vivante user space blob) and the kernel
driver / hardware.

This library uses ELF hooks to intercept only system calls such as `ioctl` and `mmap` coming from the driver, not from
other parts of the application, unlike more crude hacks using `LD_PRELOAD`.

At the beginning of the program call `the_hook`, at the end of the program call `end_hook` to finalize
and flush buffers. This should even work for native android applications that fork from the zygote.

