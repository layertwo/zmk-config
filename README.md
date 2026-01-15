# ZMK Keyboard Firmware Configuration

Custom keyboard configurations for [ZMK Firmware](https://zmk.dev) - an open-source wireless keyboard firmware built on Zephyr RTOS.

## Keyboards

This repository contains configurations for multiple custom mechanical keyboards:

### Integrated Boards
- **hennish65** - 65% keyboard with integrated nRF52840 controller

### Split Keyboards (Shields)
- **sanic62** - Split 62-key ergonomic keyboard
- **sodium62** - Split 62-key keyboard variant
- **splitreus62** - Split 62-key keyboard

All split keyboards are designed to work with nice!nano or compatible nRF52840-based controllers.

## Getting Firmware

### Automated Builds (Recommended)

Firmware is automatically built via GitHub Actions whenever changes are pushed:

1. Go to the [Actions tab](../../actions)
2. Select the latest successful workflow run
3. Download the firmware artifact (`.uf2` files)
4. Flash to your keyboard by copying the `.uf2` file to the bootloader drive

### Local Development

For advanced users who want to build locally:

```bash
# Install prerequisites
# - Python 3.8+
# - CMake 3.20+
# - Ninja build system
# - ARM GCC toolchain

# Initialize workspace
west init -l config/
west update

# Build for integrated board
west build -b hennish65

# Build for shield (split keyboards)
west build -b nice_nano -- -DSHIELD=sanic62_left
west build -b nice_nano -- -DSHIELD=sanic62_right
```

## Customizing Your Keymap

Keymaps are defined in `.keymap` files for each keyboard:

- `boards/arm/hennish65/hennish65.keymap`
- `boards/shields/sanic62/sanic62.keymap`
- `boards/shields/sodium62/sodium62.keymap`
- `boards/shields/splitreus62/splitreus62.keymap`

Edit these files to customize your layout, add layers, macros, and behaviors. See the [ZMK documentation](https://zmk.dev/docs) for keymap syntax and available features.

After committing changes, GitHub Actions will automatically build new firmware.

## Project Structure

```
.
├── boards/
│   ├── arm/
│   │   └── hennish65/          # Integrated board definition
│   └── shields/
│       ├── sanic62/            # Shield configurations
│       ├── sodium62/
│       └── splitreus62/
├── config/
│   └── west.yml                # ZMK dependency manifest
├── build.yaml                  # GitHub Actions build matrix
└── zephyr/
    └── module.yml              # Zephyr module definition
```

## Resources

- [ZMK Documentation](https://zmk.dev/docs)
- [ZMK Discord Community](https://zmk.dev/community/discord/invite)
- [ZMK GitHub Repository](https://github.com/zmkfirmware/zmk)

## Contributing

Feel free to fork this repository to create your own keyboard configurations. For issues or improvements to these specific configurations, please open an issue or pull request.

## License

This configuration repository follows the same license as ZMK Firmware (MIT).
