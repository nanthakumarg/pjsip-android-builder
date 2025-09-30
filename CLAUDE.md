# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Architecture Overview

This is a PJSIP Android Builder project that creates a complete build environment for compiling PJSIP with native libraries (OpenSSL, OpenH264, Opus, G.729) for Android. The system is designed to work in Ubuntu Docker containers or local Linux environments.

### Key Components

- **config.conf**: Central configuration file defining all library versions, build settings, target architectures, and feature toggles
- **prepare-build-system**: Environment setup script that downloads NDK, SDK, and all dependencies
- **build**: Main PJSIP compilation script that creates Android-ready libraries
- **patches/**: Directory containing PJSIP patches for specific features (fixed Call-ID, RTCP FB data export)

### Build Structure

The build system creates two main directories:
- `tools/`: Downloaded NDK, SDK, and source archives
- `output/`: Compiled libraries organized by component (openssl, openh264, opus, bcg729, pjsip)

Final PJSIP output is structured as:
```
pjsip-build-output/
├── logs/     # Build logs per architecture
├── lib/      # Compiled .so libraries per architecture
└── java/     # PJSUA Java wrapper
```

## Common Development Commands

### Initial Setup
```bash
# Configure build settings (required first step)
vi config.conf

# Setup build environment (requires sudo)
sudo ./prepare-build-system
```

### Building PJSIP
```bash
# Build complete PJSIP with all enabled libraries
./build
```

### Building Individual Libraries
```bash
# Build specific libraries only
./openssl-build-target-archs
./openh264-build-target-archs
./opus-build
./bcg729-build
```

### Docker Usage
```bash
# Recommended approach - mount repo as volume
docker run -it --name pjsip-builder -v /path/to/repo:/home ubuntu bash
```

## Configuration

### Key config.conf Settings

**Target Architectures**: `TARGET_ARCHS=("armeabi-v7a" "x86" "arm64-v8a" "x86_64")`

**Library Versions**:
- PJSIP: 2.13
- NDK: r28c
- OpenSSL: 1.1.1k
- OpenH264: 2.1.0
- Opus: 1.3.1

**Feature Toggles**: All major components have DOWNLOAD_* and ENABLE_* flags

**PJSIP-specific**:
- `USE_FIXED_CALLID`: Apply Call-ID patch
- `EXPORT_RTCP_FB_DATA`: Export RTCP feedback data
- `PJSIP_DONT_SWITCH_TO_TCP`: Force UDP transport

### Patches System

Located in `patches/`, each patch has:
- `patch.sh`: Application script
- `*.patch`: Actual patch file
- `README.md`: Patch documentation

Applied automatically based on config.conf toggles during build.

## Troubleshooting

- Build logs per architecture: `output/pjsip-build-output/logs/<arch>.log`
- Environment must be Ubuntu-based with sudo access
- All scripts expect to be run from repository root
- Docker container should mount repo at `/home` path