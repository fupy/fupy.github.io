# MicroPython on Numato Mimas V2 FPGA

## Prerequisites

 * Ubuntu 16.04 LTS `x86_64` system

 * [Numato Mimas V2](http://numato.com/mimas-v2-spartan-6-fpga-development-board-with-ddr-sdram/) Spartan6 FPGA board, with `MimasV2Config.py` set up to be able to upload "gateware" to the FPGA board (there is also a copy of  `MimasV2Config.py` installed as part of this envvironment setup  below which is used for flashing the MicroPython FPGA gateware).

 * USB A to USB Mini B cable, to connect Numato Mimas V2 to laptop

 * Xilinx ISE WebPACK installed, reachable from `/opt/Xilinx` (or optionally installed within the `Xilinx` directory *inside* your `build` directory).

Before you begin it would be a very good idea to check that the [Numato Mimas v2 `sample.bin`
code](http://productdata.numato.com/assets/downloads/fpga/mimasv2/mimasv2_sample_bin_file.bin) will run on your Mimas v2, and that you can successful replace it with a program of your own (eg, the [Numato tutorial synthesis example](https://docs.numato.com/kb/learning-fpga-verilog-beginners-guide-part-4-synthesis/).

## Gateware

### Setup

Clone repository, which originated with HDMI2USB (hence the dependencies listed):

    git clone https://github.com/upy-fpga/upy-fpga-litex-gateware

Install the relevant bits of the environment in two parts, firstly as root:

    cd upy-fpga-litex-gateware
    sudo bash -x scripts/download-env-root.sh

which will install dozens of packages as direct or indirect dependencies, including some from Tim's Ubuntu PPA.

And then as a *non-root* user (eg, your own user) for the remaining parts:

    cd upy-fpga-litex-gateware
    bash -x scripts/download-env.sh

which will download a bunch more packages, and execute/install them.  Among other things it installs `lm32` cross build tools (`binutils`, `gcc`, etc), as pre-built binary tools.  The install process is managed with `[conda](https://conda.io/docs/intro.html)`, a Python environment management tool.  (It currently actually installs a Python 3.6 environment, and then downgrades it to Python 3.5.1 for compatability, as well as a lot of other [old tools](https://github.com/upy-fpga/issues-wiki/issues/3).)

It also automatically `git clone`s the relevant [Enjoy Digital `litex` git modules](https://github.com/enjoy-digital/litex), as listed in the  [README](https://github.com/upy-fpga/upy-fpga-litex-gateware/blob/nextgen/README).

The environment install process will take several minutes, mostly depending on the download speed.

### Build

From a terminal which has *not* entered the Xilinx ISE WebPack environment, set the desired `PLATFORM` and `TARGET` to select what will be built, then enter the `upy-fpga` environment:

    cd upy-fpga-litex-gateware
    PLATFORM=mimasv2
    TARGET=base
    export PLATFORM TARGET
    source scripts/enter-env.sh

All going well, it should do some checking, report the directories being used, and then change the prompt to include the `PLATFORM` and `TARGET` values.  Eg, 

    (H2U P=mimasv2 T=base)

`make help` will show you the valid `PLATFORM` and `TARGET` values, but *cannot* be run until after `scripts/enter-env.sh` has been done; to change platforms or targets it would be best to start with a fresh terminal. ([README.targets](https://github.com/upy-fpga/upy-fpga-litex-gateware/blob/nextgen/README.targets) has some information on the possible `TARGET` values.)

From there, you can build the "gateware" for your selected `PLATFORM`/`TARGET` combination with:

    make gateware

which will result in a *lot* of output, most of it from the Xilinx ISE WebPACK tools.  This step will also take a few  minutes, and will keep your CPU pretty busy.  All going well you should end up with a `build/mimasv2_base_lm32/gateware/top.bin` file which can be loaded onto the Mimas V2.

Next you can build the "firmware" to run on the softcore CPU to provide MicroPython on the FPGA.  You can build this for your selected `PLATFORM`/`TARGET` combination with:

    make firmware

This step appears build quite quickly.  It should result in a `build/mimasv2_base_lm32//software/firmware/firmware.bin` file.

### Install

Ensure that the Numato Mimas v2 "operation mode" switch (`SW7`) is set to *program mode* -- the side nearest the USB connector is program mode (see the [Numato Mimas V2 documentation](https://docs.numato.com/doc/mimas-v2-spartan-6-fpga-development-board-with-ddr-sdram/)).

Install the gateware with:

    make gateware-flash-mimasv2

Because the upload happens at 19200 bps, this will take a couple of minutes to complete -- it does an erase cycle, a write cycle, and a read-back verification cycle. 

The process looks something like:

    (H2U P=mimasv2 T=base) ewen@parthenon:~/work/naos/src/upy-fpga/upy-fpga-litex-gateware$ make gateware-flash-mimasv2
    python $(which MimasV2Config.py) /dev/ttyACM0 build/mimasv2_base_lm32//gateware/top.bin
    ****************************************
    * Numato Lab Mimas V2 Configuration Tool *
    ****************************************
    Micron M25P16 SPI Flash detected
    Loading file build/mimasv2_base_lm32//gateware/top.bin...
    Erasing flash sectors...
    Writing to flash 100% complete...
    Verifying flash contents...
    Flash verification successful...
    Booting FPGA...
    Done.
    (H2U P=mimasv2 T=base R=nextgen) ewen@parthenon:~/work/naos/src/upy-fpga/upy-fpga-litex-gateware$ 

## Get & Build MicroPython

It needs to be built from an in-development repository with changes for MicroPython on FPGA and the Mimas v2.

Get toolchain and clone the *forked* MicroPython repository, with Mimas V2 support in it:

    (H2U P=mimasv2 T=base R=nextgen) ewen@parthenon:~/work/naos/src/upy-fpga/upy-fpga-litex-gateware$ ./scripts/build-micropython.sh

That should build fairly quickly, and result in a ``build/mimasv2_base_lm32/micropython.bin` file. 

## Installing MicroPython on the Numato Mimas v2

Return to the gateware build top directory, with the environment set up, ie as before (possibly you still have a suitable terminal open):

    cd upy-fpga-litex-gateware
    PLATFORM=mimasv2
    TARGET=base
    export PLATFORM TARGET
    source scripts/enter-env.sh

Then this custom flash image can be loaded onto the Numato Mimas v2, by ensuring that the "operation mode" switch (`SW7`) is in "program mode" (nearest to the USB connector), and then unplugging and plugging in the Numato Mimas v2 to reset it.

Once `/dev/ttyACM0` appears on the system again, run:

    MimasV2Config.py /dev/ttyACM0 build/mimasv2_base_lm32/flash.bin

to program MicroPython onto the Mimas v2.  This will take a few minutes to write, as it is uploading at 19200 bps.

The result should look something like:

    (H2U P=mimasv2 T=base R=nextgen) ewen@parthenon:~/work/naos/src/upy-fpga/upy-fpga-litex-gateware$ MimasV2Config.py /dev/ttyACM0 build/mimasv2_base_lm32/flash.bin
    ****************************************
    * Numato Lab Mimas V2 Configuration Tool *
    ****************************************
    Micron M25P16 SPI Flash detected
    Loading file build/mimasv2_base_lm32/flash.bin...
    Erasing flash sectors...
    Writing to flash 100% complete...
    Verifying flash contents...
    Flash verification successful...
    Booting FPGA...
    Done.
    (H2U P=mimasv2 T=base R=nextgen) ewen@parthenon:~/work/naos/src/upy-fpga/upy-fpga-litex-gateware$ 

