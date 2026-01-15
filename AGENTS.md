# Agent Guidelines for ZMK Keyboard Configuration

## Product Overview

This is a ZMK firmware configuration repository for custom mechanical keyboards. ZMK is an open-source keyboard firmware built on the Zephyr RTOS.

The repository contains configurations for multiple keyboard designs:
- **hennish65**: A 65% keyboard with integrated board (ARM-based)
- **sanic62**: A split 62-key keyboard (shield configuration)
- **sodium62**: A split 62-key keyboard variant (shield configuration)
- **splitreus62**: A split 62-key keyboard (shield configuration)

These configurations define keyboard layouts, key mappings, behaviors (macros, tap dances), and hardware specifications for wireless mechanical keyboards using Nordic nRF52840 controllers.

## Technology Stack

### Build System
- **West**: Zephyr's meta-tool for managing multi-repository projects
- **CMake**: Build system for compiling firmware
- **GitHub Actions**: CI/CD pipeline defined in `build.yaml`

### Frameworks & Dependencies
- **ZMK Firmware**: Main firmware framework (https://github.com/zmkfirmware/zmk)
- **Zephyr RTOS**: Real-time operating system foundation
- **Device Tree**: Hardware description language for board/shield definitions

### Hardware Platform
- **Nordic nRF52840**: ARM Cortex-M4 microcontroller
- **nice!nano**: Popular nRF52840-based controller board

### Key Technologies
- Device Tree Source (`.dts`, `.dtsi`, `.overlay`) for hardware configuration
- Kconfig for build-time configuration
- C preprocessor for keymap definitions

### Common Commands

#### Building Firmware
Builds are automated via GitHub Actions based on `build.yaml` configuration. The workflow compiles firmware for all board/shield combinations defined in the matrix.

#### Local Development
```bash
# Initialize west workspace
west init -l config/

# Update dependencies
west update

# Build for specific board
west build -b hennish65

# Build for shield + board combination
west build -b nice_nano -- -DSHIELD=sanic62_left
```

#### Configuration Files
- `build.yaml`: Defines build matrix for CI/CD
- `config/west.yml`: West manifest for dependency management
- Board configs: `config/boards/arm/<board>/`
- Shield configs: `config/boards/shields/<shield>/`

## Development Workflow

### Making Changes
1. Modify keymap files (`.keymap`) for layout changes
2. Edit device tree files (`.dts`, `.dtsi`, `.overlay`) for hardware changes
3. Update Kconfig files (`.conf`, `Kconfig.*`) for feature configuration
4. Commit changes to trigger automated builds via GitHub Actions

### Testing Changes
- GitHub Actions builds all configurations defined in `build.yaml`
- Artifacts are available in the Actions tab after successful builds
- Flash `.uf2` files to keyboard bootloader for testing

### Common Modifications

#### Adding a New Layer
Edit the `.keymap` file and add a new layer definition within the `keymap` node.

#### Changing Key Bindings
Modify bindings in the `.keymap` file using ZMK behavior syntax (e.g., `&kp`, `&mo`, `&lt`).

#### Enabling Features
Add Kconfig options to `.conf` files (e.g., `CONFIG_ZMK_RGB_UNDERGLOW=y`).

#### Hardware Changes
Modify device tree files to change pin assignments, matrix definitions, or add hardware features.

## Project Structure

### Root Level
- `build.yaml`: GitHub Actions build matrix defining board/shield combinations
- `config/`: West workspace configuration
- `boards/`: All keyboard hardware and keymap definitions
- `.github/workflows/`: CI/CD pipeline configuration

### Board Definitions (`config/boards/arm/`)
Complete keyboard implementations with integrated controllers.

**Structure per board:**
```
config/boards/arm/<board_name>/
├── CMakeLists.txt          # Build configuration
├── Kconfig.board           # Board selection config
├── Kconfig.defconfig       # Default configuration
├── board.cmake             # Board-specific build settings
├── <board>.conf            # Kconfig settings
├── <board>.dts             # Device tree source (hardware definition)
├── <board>.dtsi            # Device tree include (shared definitions)
├── <board>.keymap          # Key layout and behavior definitions
├── <board>.yaml            # Board metadata
└── <board>_defconfig       # Default Kconfig values
```

### Shield Definitions (`config/boards/shields/`)
Keyboard PCB configurations designed to work with separate controller boards (e.g., nice!nano).

**Structure per shield:**
```
config/boards/shields/<shield_name>/
├── Kconfig.defconfig       # Default configuration
├── Kconfig.shield          # Shield selection config
├── <shield>.conf           # Shared Kconfig settings
├── <shield>.dtsi           # Shared device tree definitions
├── <shield>.keymap         # Key layout and behaviors
├── <shield>.zmk.yml        # ZMK metadata
├── <shield>_left.conf      # Left half specific config (split keyboards)
├── <shield>_left.overlay   # Left half hardware definition
├── <shield>_right.conf     # Right half specific config (split keyboards)
└── <shield>_right.overlay  # Right half hardware definition
```

### West Configuration
- `config/west.yml`: Manifest defining ZMK firmware dependency from upstream repository

### File Type Conventions

#### Device Tree Files
- `.dts`: Complete device tree source (board definitions)
- `.dtsi`: Device tree include files (shared/reusable definitions)
- `.overlay`: Device tree overlay (shield-specific hardware additions)

#### Configuration Files
- `.conf`: Kconfig configuration settings
- `_defconfig`: Default Kconfig values
- `Kconfig.*`: Kconfig menu definitions

#### Keymap Files
- `.keymap`: Keyboard layout, layers, macros, and behavior definitions
- Uses C preprocessor syntax with ZMK-specific bindings

### Split Keyboard Pattern
Split keyboards require separate configurations for left and right halves:
- `<name>_left.overlay` + `<name>_left.conf`
- `<name>_right.overlay` + `<name>_right.conf`
- Shared definitions in `<name>.dtsi` and `<name>.keymap`

Both halves are built separately and defined in `build.yaml` matrix.

## Key Concepts for AI Agents

### Device Tree Hierarchy
- Board `.dts` files define complete hardware (integrated boards)
- Shield `.overlay` files add hardware definitions to base controller boards
- `.dtsi` files contain shared/reusable definitions included by other files
- Device tree uses a hierarchical node structure with properties and labels

### Keymap Syntax
- Keymaps use C preprocessor syntax with ZMK-specific macros
- Bindings reference behaviors: `&kp KEY` (key press), `&mo LAYER` (momentary layer)
- Layers are defined in order, with layer 0 as the default
- Macros and custom behaviors can be defined in the keymap

### Configuration Precedence
1. Board/shield specific `.conf` files
2. `Kconfig.defconfig` files (defaults)
3. `Kconfig` menu selections
4. Built-in ZMK defaults

### Build Matrix
The `build.yaml` file defines what gets built:
- `board`: Array of integrated boards to build
- `include`: Array of board+shield combinations for shields
- Each entry generates a separate firmware artifact

### Common Pitfalls
- Device tree syntax is strict (semicolons, commas, angle brackets matter)
- Kconfig options must be valid (check ZMK documentation)
- Split keyboards need matching left/right configurations
- Pin numbers are controller-specific (nice!nano uses different numbering than raw nRF52840)
- Matrix row/col definitions must match physical wiring

### Debugging Tips
- Check GitHub Actions logs for build errors
- Device tree errors often indicate syntax or reference issues
- Kconfig errors suggest invalid or conflicting options
- Missing bindings in keymaps cause compilation failures
- Use ZMK Discord for community support on complex issues
