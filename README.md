# Transforming Detective - macOS Installation

## Running the Game

Double-click `Game.app` to launch. If you encounter issues, follow the troubleshooting steps below.

## Building from Source

### Prerequisites

- [Node.js](https://nodejs.org/) (v14 or later)
- [NW.js SDK](https://nwjs.io/) (v0.65.0 or compatible version)
- [npm](https://www.npmjs.com/) or [yarn](https://yarnpkg.com/)

### Build Steps

1. **Install dependencies**:
   ```bash
   yarn install
   ```

2. **Run in development mode**:
   ```bash
   # Using NW.js directly
   /path/to/nwjs .
   ```

3. **Package for distribution**:
   ```bash
   # Install nw-builder globally
   yarn global add nw-builder

   # Build for macOS
   nwbuild -p osx64 -o dist/ .
   ```

4. **Fix permissions on the built app** (required for macOS):
   ```bash
   chmod +x dist/Game/osx64/Game.app/Contents/MacOS/*
   chmod +x dist/Game/osx64/Game.app/Contents/Versions/*/nwjs\ Framework.framework/Helpers/*
   ```

### Project Structure

```
.
├── Game.app/              # Packaged macOS application
│   └── Contents/
│       ├── MacOS/         # Main executable (nwjs)
│       ├── Resources/     # App resources and game assets
│       └── Versions/      # NW.js framework and helpers
├── package.json           # NW.js manifest and dependencies
└── README.md
```

## Troubleshooting

### "Permission denied" error

macOS may block execution of downloaded applications. Run these commands in Terminal:

```bash
# Remove quarantine flag (required for unsigned apps downloaded from the internet)
xattr -cr /path/to/TransformingDetective_mac_v014/Game.app

# Grant execute permissions to the main executable
chmod +x /path/to/TransformingDetective_mac_v014/Game.app/Contents/MacOS/*

# Grant execute permissions to helper binaries (fixes crashpad_handler errors)
chmod +x /path/to/TransformingDetective_mac_v014/Game.app/Contents/Versions/*/nwjs\ Framework.framework/Helpers/*
```

Replace `/path/to/` with the actual path to your game folder.

### One-liner fix

Run this single command to apply all fixes at once:

```bash
xattr -cr Game.app && chmod -R +x Game.app/Contents/MacOS Game.app/Contents/Versions/*/nwjs\ Framework.framework/Helpers
```

### Gatekeeper warning

If macOS shows "App can't be opened because it is from an unidentified developer":

1. Open **System Settings** → **Privacy & Security**
2. Scroll down and click **Open Anyway** next to the blocked app message
3. Alternatively, right-click the app and select **Open** from the context menu

## Technical Details

This application is built with NW.js (Node WebKit). The permission issues occur because:

1. **Quarantine attribute**: macOS adds `com.apple.quarantine` extended attributes to files downloaded from the internet
2. **Execute permissions**: Archive extraction may not preserve Unix execute permissions on binaries

## System Requirements

- macOS 10.13 or later
- x86_64 architecture (Intel Mac or Apple Silicon with Rosetta 2)
