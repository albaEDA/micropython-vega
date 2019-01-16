[![Build Status](https://travis-ci.org/micropython/micropython.png?branch=master)](https://travis-ci.org/micropython/micropython) [![Coverage Status](https://coveralls.io/repos/micropython/micropython/badge.png?branch=master)](https://coveralls.io/r/micropython/micropython?branch=master)

The MicroPython project
=======================
<p align="center">
  <img src="https://open-isa.org/wp-content/uploads/2018/11/orderHero.png" alt="MicroPython Logo"/>
</p>

This is the MicroPython project, which aims to put an implementation
of Python 3.x on microcontrollers and small embedded systems.
You can find the official website at [micropython.org](http://www.micropython.org).

WARNING: this project is in beta stage and is subject to changes of the
code-base, including project-wide name changes and API changes.

MicroPython implements the entire Python 3.4 syntax (including exceptions,
`with`, `yield from`, etc., and additionally `async`/`await` keywords from
Python 3.5). The following core datatypes are provided: `str` (including
basic Unicode support), `bytes`, `bytearray`, `tuple`, `list`, `dict`, `set`,
`frozenset`, `array.array`, `collections.namedtuple`, classes and instances.
Builtin modules include `sys`, `time`, and `struct`, etc. Select ports have
support for `_thread` module (multithreading). Note that only a subset of
Python 3 functionality is implemented for the data types and modules.

MicroPython can execute scripts in textual source form or from precompiled
bytecode, in both cases either from an on-device filesystem or "frozen" into
the MicroPython executable.

See the repository http://github.com/micropython/pyboard for the MicroPython
board (PyBoard), the officially supported reference electronic circuit board.

Major components in this repository:
- py/ -- the core Python implementation, including compiler, runtime, and
  core library.
- mpy-cross/ -- the MicroPython cross-compiler which is used to turn scripts
  into precompiled bytecode.
- ports/vega-pulpino/ -- A minimal example of Micropython for RISCV
- tests/ -- test framework and test scripts.
- docs/ -- user documentation in Sphinx reStructuredText format. Rendered
  HTML documentation is available at http://docs.micropython.org.

Additional components:
- ports/bare-arm/ -- a bare minimum version of MicroPython for ARM MCUs. Used
  mostly to control code size.
- ports/teensy/ -- a version of MicroPython that runs on the Teensy 3.1
  (preliminary but functional).
- ports/pic16bit/ -- a version of MicroPython for 16-bit PIC microcontrollers.
- ports/cc3200/ -- a version of MicroPython that runs on the CC3200 from TI.
- ports/esp8266/ -- a version of MicroPython that runs on Espressif's ESP8266 SoC.
- ports/esp32/ -- a version of MicroPython that runs on Espressif's ESP32 SoC.
- ports/nrf/ -- a version of MicroPython that runs on Nordic's nRF51 and nRF52 MCUs.
- extmod/ -- additional (non-core) modules implemented in C.
- tools/ -- various tools, including the pyboard.py module.
- examples/ -- a few example Python scripts.

The subdirectories above may include READMEs with additional info.

"make" is used to build the components, or "gmake" on BSD-based systems.
You will also need bash, gcc, and Python (at least 2.7 or 3.3).

The VEGA board version
----------------

The "vega-pulpino" port requires the Vega-SDK (https://open-isa.org/downloads/) 
under installers, you can choose either windows or linux. Windows has been untested.
The Makefile assumes the SDK is placed under /opt/VEGA_SDK but this can be overwritten
with VEGA_SDK = ...

The OpenSDA does not seem to flash a .bin so all instructions will assume an external
debugger. The supplied cfg uses an altera-usb blaster which can be obtained easily.

If you do not have a toolchain setup and would like to try, find a precompiled elf here:
https://github.com/AaronKel/micropython-vega/releases/tag/0.1

To build:

    $ cd ports/vega-pulpino
    $ VEGA_SDK = <LOCATION OF VEGA SDK>
    $ make

Then to give it a try (Each $ is a different terminal - Steps 1 and 2 need only be performed once):

    $ openocd -f rv32m1_ri5cy.cfg
    $ riscv32-unknown-elf-gdb micropython.elf
    >>> target remote:3333
    >>> load
    $ sudo screen /dev/ttyACM0 115200
    >>> list(5 * x + y for x in range(10) for y in [4, 2, 1])

Browse available modules on
[PyPI](https://pypi.python.org/pypi?%3Aaction=search&term=micropython).
Standard library modules come from
[micropython-lib](https://github.com/micropython/micropython-lib) project.

External dependencies
---------------------

Building MicroPython ports may require some dependencies installed.

For Unix port, `libffi` library and `pkg-config` tool are required. On
Debian/Ubuntu/Mint derivative Linux distros, install `build-essential`
(includes toolchain and make), `libffi-dev`, and `pkg-config` packages.

Other dependencies can be built together with MicroPython. This may
be required to enable extra features or capabilities, and in recent
versions of MicroPython, these may be enabled by default. To build
these additional dependencies, first fetch git submodules for them:

    $ git submodule update --init

Use the same command to get the latest versions of dependencies, as
they are updated from time to time. After that, in the port directory
(e.g. `ports/unix/`), execute:

    $ make deplibs

This will build all available dependencies (regardless whether they
are used or not). If you intend to build MicroPython with additional
options (like cross-compiling), the same set of options should be passed
to `make deplibs`. To actually enable/disable use of dependencies, edit
`ports/unix/mpconfigport.mk` file, which has inline descriptions of the options.
For example, to build SSL module (required for `upip` tool described above,
and so enabled by dfeault), `MICROPY_PY_USSL` should be set to 1.

For some ports, building required dependences is transparent, and happens
automatically. They still need to be fetched with the git submodule command
above.

The STM32 version
-----------------

The "stm32" port requires an ARM compiler, arm-none-eabi-gcc, and associated
bin-utils.  For those using Arch Linux, you need arm-none-eabi-binutils,
arm-none-eabi-gcc and arm-none-eabi-newlib packages.  Otherwise, try here:
https://launchpad.net/gcc-arm-embedded

To build:

    $ git submodule update --init
    $ cd ports/stm32
    $ make

You then need to get your board into DFU mode.  On the pyboard, connect the
3V3 pin to the P1/DFU pin with a wire (on PYBv1.0 they are next to each other
on the bottom left of the board, second row from the bottom).

Then to flash the code via USB DFU to your device:

    $ make deploy

This will use the included `tools/pydfu.py` script.  If flashing the firmware
does not work it may be because you don't have the correct permissions, and
need to use `sudo make deploy`.
See the README.md file in the ports/stm32/ directory for further details.

Contributing
------------

MicroPython is an open-source project and welcomes contributions. To be
productive, please be sure to follow the
[Contributors' Guidelines](https://github.com/micropython/micropython/wiki/ContributorGuidelines)
and the [Code Conventions](https://github.com/micropython/micropython/blob/master/CODECONVENTIONS.md).
Note that MicroPython is licenced under the MIT license, and all contributions
should follow this license.
