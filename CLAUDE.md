# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Chat Tools is a Fabric mod for Minecraft that provides customizable chat-related features. It supports multiple Minecraft versions (1.16.5 through 1.21.11) using preprocessor macros for multi-version compatibility.

## Build Commands

```bash
# Build all versions and gather JARs into build/libs/
./gradlew buildAndGather

# Build a specific Minecraft version
./gradlew :1.21.11:build

# Run the game client for a specific version
./gradlew :1.21.11:runClient
```

The built JARs are collected into `build/libs/v{mod_version}/` with filenames like `chattools-{mc_version}-v{mod_version}.jar`.

## Architecture

### Multi-Version System

- Uses [Fallen-Breath/preprocessor](https://github.com/Fallen-Breath/preprocessor) for macro-based multi-version support
- `settings.json` lists all supported Minecraft versions
- Each version has its own `versions/{mc_version}/gradle.properties` with version-specific dependencies
- `common.gradle` contains the shared build logic for all versions
- Source code uses preprocessor directives (e.g., `//#if MC >= 11800`) for version-specific code paths

### Code Structure

- **Entry point**: `net.apple70cents.chattools.ChatTools` implements `ModInitializer`
- **Configuration**: `config/` - Config GUI generation using Cloth Config, storage in `chat_tools.json`
- **Features**: `features/` - Each feature is self-contained:
  - `bubble/` - Chat bubble rendering above player heads
  - `chatkeybindings/` - Command macros and repeat hotkeys
  - `filter/` - Chat message filtering
  - `formatter/` - Auto-formatting outgoing messages with color codes/suffixes
  - `general/` - Timestamps, nickname hiding, chat history, translator
  - `notifier/` - Toast notifications, sound alerts, highlighting
  - `responder/` - Auto-reply to specific chat patterns
  - `translator/` - Multiple translator backends (Google, Baidu, Microsoft, builtin)
- **Mixins**: `mixins/` - Fabric Mixin classes for hooking into Minecraft internals
- **Utilities**: `utils/` - Shared helper classes

### Configuration System

- Default config: `src/main/resources/assets/chattools/default_config.json`
- GUI definition: `src/main/resources/assets/chattools/config_gui.json`
- Config keys follow pattern: `section.FeatureName.Property` (e.g., `general.ChatTools.Enabled`)
- Translations via yamlang plugin in `assets/chattools/lang/`

### Dependencies

- Fabric API
- Cloth Config (for configuration GUI)
- Mod Menu (optional, for settings access)
- MixinExtras
- two-slices (for toast notifications)

## Key Technical Details

- Client-side only mod (`"environment": "client"`)
- Session Identifier: save name (singleplayer) or server address (multiplayer) - used for per-server rules
- Regular expressions use MULTILINE mode with `find()` method
- Color codes (section sign patterns) are stripped when processing messages
