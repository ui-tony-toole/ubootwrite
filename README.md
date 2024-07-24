# ubootwrite

**ubootwrite** is a simple Python tool that uploads binary images to the RAM of Linux systems running the U-Boot bootloader (e.g., for OpenWRT) via the serial port. It is useful when no alternative transfer methods (XMODEM/TFTP/BOOTM/etc.) are available. Due to converting the binary file to ASCII and sending it in 32-bit chunks, ubootwrite has a high overhead, making the transfer of large amounts of data slow.

The original author is "pgid69", and the source can be found on the [OpenWRT forum](https://forum.openwrt.org/viewtopic.php?pid=183315#p183315). The initial commit is the original version from the forum, and "pgid69" states that the tool is based on [brntool](https://github.com/rvalles/brntool). I made some adjustments for uploading data to the Arcadyan ARV752DPW22 (Easybox 803A) and decided to "fork" the code here as it did not have a permanent home.

This project was forked from [Paulius Zaleckas' ubootwrite](https://github.com/pauliuszaleckas/ubootwrite).

## License

This tool is licensed under the [GPLv3](http://opensource.org/licenses/GPL-3.0), following the license of [brntool](https://github.com/rvalles/brntool). See [LICENSE.md](LICENSE.md) for more details.

## Dependencies

- Python 3
- pyserial

## Overview

Use Python (preferably 3.0+) to run the tool:

```sh
python ubootwrite.py [OPTIONS]
```

### Options

- `--verbose` - Enable verbose output.
- `--serial` - Specify the serial port to write to, e.g., `--serial=/dev/ttyUSB2`. It opens with 115200 baud, 8 data bits, and one stop bit.
- `--write` - Specify the file to transfer to RAM, e.g., `--write=openwrt-squashfs.image`.
- `--addr` - Specify the RAM start address to write to, e.g., `--addr=0x80050000`.
- `--size` - Specify the number of bytes to transfer, e.g., `--size=12345`. Omit to transfer the entire file.
- `--big` - Specify if the target is big-endian (default is little-endian).

**Example command:**

```sh
python ubootwrite.py --serial=/dev/ttyUSB6 --write=openwrt-squashfs.image --addr=0x80050000
```

Note: This process can take a long time. Be patient. Once the data is in RAM, you can copy it to flash by:

1. Unprotecting flash: `protect off all`
2. Erasing the sectors: `erase [ADDRESS_IN_FLASH] +[SIZE_OF_DATA]` (all in hex)
3. Copying the data to flash: `cp.b [RAM_ADDRESS] [ADDRESS_IN_FLASH] [SIZE_OF_DATA]` (all in hex)

## MacOS Setup

**Getting up and running on MacOS with Homebrew:**

To install `pyserial` via `pip`:

```sh
# From within a temporary folder, run
python3 -m venv venv
source venv/bin/activate
python3 -m pip install pyserial
```

**Example command:**

```sh
python ubootwrite.py --serial=/dev/cu.usbserial-FT6TUI7Q --write=u-boot.mbn --addr=0x44000000
```

## FAQ

**Q:** I'm on Linux and cannot access the serial port. What should I do?
**A:** You might need to add your username to the dialout group: `sudo usermod -a -G dialout USERNAME` or use sudo.

## Reporting Bugs or Suggestions

The best way to report a bug or suggest an improvement is to post an issue on GitHub. Make your report simple yet descriptive and include all the information needed to reproduce the bug. **"Does not work" is not enough!** If you cannot compile, please state your system, compiler version, etc. You can also contact me via email if needed.
