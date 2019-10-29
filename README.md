# GettingStarted
How to set up a dev environment for this project

## Arduino IDE
This IDE works on any OS!
Follow the steps outlined at [this url](https://learn.adafruit.com/adafruit-feather-m0-basic-proto/setup) to add the Adafruit board support package to Arduino.
Then follow the steps at this [other url](https://learn.adafruit.com/adafruit-feather-m0-basic-proto/using-with-arduino-ide) to set up an Arduino sketch that supports the Feather M0 Proto board.

## Rust

The setup for Rust is the same for all platforms with the minor exception of installing the gcc-arm tooling.

### Installation

1. Install Rust by using rustup (available [here](https://rustup.rs/))
2. Install gcc-arm tooling
   1. Ubuntu: `# apt install gcc-arm-embedded`
   2. Windows: ???
3. Open a terminal session and install the Rust toolchain for this chip. I'm not sure if this is an OS dependent command!
   1. `rustup target add thumbv6m-none-eabi`
4. Install BOSSA
   1. Use the BOSSA version that comes with the Arduino IDE. This is what I ended up doing.
      1. Ubuntu: `~/.arduino15/packages/arduino/tools/bossac/1.7.0/bossac`
   2. Ubuntu: Compile from source https://github.com/shumatech/BOSSA
   3. Windows: Download binary from https://github.com/shumatech/BOSSA/releases

That's it!

### Compiling Example Projects

1. Open up a terminal session and `cd` to your preferred directory for project files.
2. Use git to clone this repo https://github.com/atsamd-rs/atsamd.
3. `cd` into the `boards/feather_m0` directory.
   1. Take note of the `memory.x` file. It's not obvious *how* it works but this file ensures that the compiler knows the addresses for flash and ram and, most importantly, the start address for application code.
   2. If you compile a future project with an incorrect `memory.x` file, you may accidentally wipe the bootloader!
4. Build an example project, see `examples` directory. This command will generate output files in the newly created `target` build directory.
   1. `cargo build --example blinky_basic`
5. Use arm tooling to convert the ELF file to a BIN file (Arduino IDE does this automatically). This command *might* work on Windows.
   1. `arm-none-eabi-objcopy -O binary target/thumbv6m-none-eabi/debug/examples/blinky_basic target/thumbv6m-none-eabi/debug/examples/blinky_basic.bin`

### Flashing the FeatherM0 Proto Board

1. Put the Feather M0 into bootloader/DFU mode by double clicking the master reset button. You should see the LED next to the MicroUSB port fade in and out if done correctly.
2. Plug the Feather M0 into your computer and identify which serial port it's on.
   1. Ubuntu: Likely `/dev/ttyACM0`
   2. Windows: Some COM port of the form `COM#`
3. Flash your compiled and converted BIN file to the board.
   1. `/path/to/bossac -i -d --port=PORT_HERE -U true -i -e -w -v target/thumbv6m-none-eabi/debug/examples/blinky_basic.bin -R`
   2. On Ubuntu, `PORT_HERE` should be replaced with the serial port WITHOUT `/dev/`. If your serial port is `/dev/ttyACM0` then `PORT_HERE` should be replaced with `ttyACM0`.