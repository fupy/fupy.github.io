## Welcome to MicroPython on FPGAs

* [MicroPython](http://micropython.org/)
* [FPGA](https://en.wikipedia.org/wiki/Field-programmable_gate_array)

The aim of this project is to make MicroPython run on FPGAs using the [LiteX](https://github.com/enjoy-digital/litex) & [Migen+MiSoC](http://m-labs.hk/gateware.html) technologies. This allows you to do full stack development (FPGA gateware & soft CPU firmware) in Python!

## Gateware

`Gateware` is the name we use for the hardware code that is loaded onto the FPGA that MicroPython runs on.

For MicroPython development we use a slightly modified version of the [`HDMI2USB-litex-firmware`](https://github.com/timvideos/HDMI2USB-litex-firmware) from the [HDMI2USB project](https://hdmi2usb.tv).

## Getting Started

 * Ewen wrote [detailed instructions for the MimasV2](MimasV2.md)

### Developing in MicroPython environment

 * get lm32 toolchain
 * git clone https://github.com/upy-fpga/micropython.git
 * cd litex
 * Download prebuilt gateware + headers using `get-gateware.sh`
 * Compile micropython
 * Load micropython

### Developing in HDMI2USB LiteX environment

 1. Follow [getting started instructions](https://github.com/upy-fpga/upy-fpga-litex-gateware/blob/master/getting-started.md) to setup gateware environment.
 2. Enter the gateware environment with ./scripts/enter-env.sh
 3. Set PLATFORM and TARGET correctly - IE "export PLATFORM=mimasv2" if using the MimasV2
 4. Build the gateware with `make gateware`
 5. Run ./scripts/build-micropython.sh

This will give you an image for your target at `./build/$PLATFORM_$TARGET_lm32/micropython.bin` which you can then flash to your board.

#### Running MicroPython inside QEmu environment

The HDMI2USB LiteX environment provides some limited QEmu emulation of the FPGA gateware, this means you can test your code without needing hardware.

It can be used with the MicroPython image by running `./scripts/build-qemu.sh` and then replacing `-kernel qemu.bin` with `-kernel micropython.bin` in the last command.

See the [TimVideos' QEmu for LiteX GitHub Repo](https://github.com/timvideos/qemu-litex/blob/master/README.md)

## More Information

 * [Wiki](https://github.com/upy-fpga/issues-wiki/wiki)
 * [GitHub Issues](https://github.com/upy-fpga/issues-wiki/issues)

## Contact

 * [MicroPython on FPGA Mailing List](https://groups.google.com/forum/#!forum/upy-fpga/join)
 * [MicroPython on FPGA Chat Channel, #upy-fpga on FreeNode](irc://irc.freenode.net/#upy-fpga) ([WebChat](https://webchat.freenode.net/?channels=#upy-fpga))

## Current Targets

### Hardware Targets

 * [MimasV2](http://numato.com/mimas-v2-spartan-6-fpga-development-board-with-ddr-sdram/) - $50 USD, Spartan 6 board with DDR memory.
 
 * Future [MicroPython on FPGA targets are documented in this spreadsheet](https://docs.google.com/spreadsheets/d/10aMU7oPXAhfRbQEVXkMQlRsin2yCkN39B6W1KXiR6d4/edit#gid=0).
 
### Software Targets

These targets don't need any physical hardware and are good for testing / developing.

 * [LiteX QEmu emulation](https://github.com/shenki/qemu-litex) (see [MicroPython on FPGAs Wiki Page](https://github.com/shenki/micropython/wiki/Micropython-on-FPGAs) for notes on getting started)
 * [Verilator Verilog Simulator](https://www.veripool.org/wiki/verilator)
