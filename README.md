# Microsemi AloeVera Linux Software Development Kit

This builds a complete RISC-V cross-compile toolchain for the Microsemi 
AloeVera Linux Software Development Kit. It also builds a `bbl` image for 
booting the development board.

The complete User Guide for the MPFS-DEV-KIT is available as a PDF in the `doc/` subdirectory.

## Tested Configurations

### Ubuntu 18.04 x86_64 host

- Status: Not working, automake build fails.
- Build dependencies: `build-essential git autotools texinfo bison flex
  libgmp-dev libmpfr-dev libmpc-dev gawk libz-dev libssl-dev`
- Additional build deps for QEMU: `libglib2.0-dev libpixman-1-dev`
- Additional build deps for Spike: `device-tree-compiler`
- tools require for 'format-boot-loader' target: mtools
- 
### Ubuntu 16.04 x86_64 host

- Status: Needs to be tested
- Build dependencies: `build-essential git autotools texinfo bison flex
  libgmp-dev libmpfr-dev libmpc-dev gawk libz-dev libssl-dev`
- Additional build deps for QEMU: `libglib2.0-dev libpixman-1-dev`
- Additional build deps for Spike: `device-tree-compiler`
- tools require for 'format-boot-loader' target: mtools

## Build Instructions

Checkout this repository. Then you will need to checkout all of the linked submodules using:

`git submodule update --recursive --init`

This will take some time and require around 7GB of disk space. Some modules may fail because certain dependencies don't have the best git hosting. The only solution is to wait and try again later (or ask someone for a copy of that source repository).

Once the submodules are initialized, run `make` and the complete toolchain and bbl image will be built. The completed build tree will consume about 14G of disk space.

## Upgrading the BBL for booting the Freedom Unleashed dev board

Once the build of the SDK is complete, there will be a new bbl image under `work/bbl.bin`. This can be copied to the first partition of the MicroSD card using the `dd` tool.

To completely erase, reformat, and program a disk, with the label `sdX`, run:
`sudo make DISK=/dev/sdX format-boot-loader`. This depends on gdisk and e2fsprogs.

## Booting Linux on a simulator

You can boot linux on qemu by running `make qemu`.

You can boot linux on spike by running `make sim`.  This requires a patch to
enable the old serial driver, because the new one which works best on the
Freedom Unleashed hardware unfortunately does not work on spike.

```
diff --git a/conf/linux_defconfig b/conf/linux_defconfig
index cd87340..87b480f 100644
--- a/conf/linux_defconfig
+++ b/conf/linux_defconfig
@@ -53,7 +53,7 @@ CONFIG_SERIAL_8250_CONSOLE=y
 CONFIG_SERIAL_OF_PLATFORM=y
 CONFIG_SERIAL_SIFIVE=y
 CONFIG_SERIAL_SIFIVE_CONSOLE=y
-# CONFIG_HVC_RISCV_SBI is not set
+CONFIG_HVC_RISCV_SBI=y
 CONFIG_VIRTIO_CONSOLE=y
 # CONFIG_HW_RANDOM is not set
 CONFIG_I2C=y