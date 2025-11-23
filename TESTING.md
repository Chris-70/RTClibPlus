# Testing RTClib

This document explains how to test the RTClib library to ensure it works correctly.

## Overview

RTClib is an Arduino library, and testing consists of:
1. **Compilation tests** - Verifying that example sketches compile successfully
2. **Code formatting checks** - Ensuring code follows the LLVM style guide
3. **Hardware testing** - Running examples on actual Arduino hardware (optional)

## Automated Testing (CI)

The library uses GitHub Actions for continuous integration. The CI automatically runs on every pull request and push. You can see the CI configuration in `.github/workflows/githubci.yml`.

The CI performs:
- Compilation of all example sketches across multiple Arduino platforms
- Code formatting verification using clang-format
- Documentation generation using Doxygen

## Running Tests Locally

### 1. Compilation Tests

To test that the library compiles correctly, you can compile the example sketches using the Arduino IDE or command-line tools.

#### Using Arduino IDE:
1. Install the library in your Arduino libraries folder
2. Open Arduino IDE
3. Go to File > Examples > RTClib
4. Select any example (e.g., `ds3231`, `pcf8523`, `datecalc`)
5. Click "Verify" (checkmark button) to compile
6. Repeat for other examples to ensure they all compile

#### Using Arduino CLI:
If you have [Arduino CLI](https://arduino.github.io/arduino-cli/) installed:

```bash
# Install required dependencies
arduino-cli lib install "Adafruit BusIO"

# Compile an example (replace with your board)
arduino-cli compile --fqbn arduino:avr:uno examples/ds3231

# Compile all examples
for example in examples/*/; do
  arduino-cli compile --fqbn arduino:avr:uno "$example"
done
```

#### Using Adafruit's CI Tools:
The library uses Adafruit's CI tools for testing. To run the same tests locally:

```bash
# Clone the Adafruit CI repository
git clone https://github.com/adafruit/ci-arduino.git ci

# Install dependencies
bash ci/actions_install.sh

# Run compilation tests for main platforms
python3 ci/build_platform.py main_platforms
```

### 2. Code Formatting Tests

The library follows the LLVM Coding Standards. To check code formatting:

#### Install clang-format:
- **Ubuntu/Debian**: `sudo apt-get install clang-format`
- **macOS**: `brew install clang-format`
- **Windows**: Download from [LLVM releases](https://releases.llvm.org/)

#### Check formatting:
```bash
# Check all files (using Adafruit's CI script)
python3 ci/run-clang-format.py -e "ci/*" -e "bin/*" -r .
```

#### Format a file:
```bash
clang-format -i src/RTClib.cpp
```

### 3. Hardware Testing

For complete testing, you should run examples on actual hardware:

1. Connect your RTC module (DS3231, PCF8523, DS1307, or PCF8563) to your Arduino
2. Upload an example sketch appropriate for your RTC module
3. Open the Serial Monitor (115200 baud for most examples)
4. Verify the output shows correct date/time information

#### Recommended test examples:
- **DS3231**: `examples/ds3231/ds3231.ino`
- **PCF8523**: `examples/pcf8523/pcf8523.ino`
- **DS1307**: `examples/ds1307/ds1307.ino`
- **PCF8563**: `examples/pcf8563/pcf8563.ino`

## Before Submitting a Pull Request

Please ensure:
1. ✅ All example sketches compile without errors
2. ✅ Code follows the LLVM formatting standards (run clang-format)
3. ✅ If possible, test on actual hardware with your RTC module
4. ✅ Document any known limitations or platform-specific issues

## Continuous Integration Details

The GitHub Actions workflow (`.github/workflows/githubci.yml`) automatically:
- Tests compilation on multiple Arduino platforms (AVR, SAMD, ESP8266, etc.)
- Verifies code formatting
- Generates API documentation with Doxygen

You can view the CI results on any pull request or in the Actions tab of the repository.

## Troubleshooting

### Compilation Errors
- Ensure you have the **Adafruit BusIO** library installed (required dependency)
- Check that you're using a compatible Arduino board
- Verify your Arduino IDE or CLI is up to date

### Formatting Issues
- Run `clang-format -i <file>` to automatically fix formatting
- The CI uses clang-format with LLVM style defaults

### Hardware Issues
- Double-check I2C wiring (SDA/SCL connections)
- Verify your RTC module has a battery and is powered
- Check that the I2C address matches your module (DS3231: 0x68, PCF8523: 0x68)

## Additional Resources

- [Arduino Library Testing Guide](https://learn.adafruit.com/the-well-automated-arduino-library)
- [Adafruit CI Arduino Documentation](https://github.com/adafruit/ci-arduino)
- [LLVM Coding Standards](https://llvm.org/docs/CodingStandards.html)
