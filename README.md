## Welcome to MicroPython on FPGAs

* [MicroPython](http://micropython.org/)
* [FPGA](https://en.wikipedia.org/wiki/Field-programmable_gate_array)

The aim of this project is to make MicroPython run on FPGAs using the [LiteX] & [Migen+MiSoC] technologies. This allows you to do full stack development (FPGA gateware & soft CPU firmware) in Python!

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
