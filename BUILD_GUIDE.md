# Plotter Build & Distribution Guide

## Automated Builds with GitHub Actions

The project is set up for automated cross-platform builds using GitHub Actions.

### How It Works

1. **Push code to GitHub**
2. **Create a release tag:**
   ```bash
   git tag v1.0.0
   git push origin v1.0.0
   ```
3. **GitHub Actions automatically:**
   - ✅ Builds Windows MSI installer
   - ✅ Builds macOS .dmg (Intel & Apple Silicon)
   - ✅ Builds Linux AppImage
   - ✅ Creates GitHub Release with all installers

### Release Artifacts

When a tag is created, GitHub Actions produces:
- **Plotter-Setup.msi** - Windows installer
- **Plotter.dmg** - macOS installer
- **Plotter-x86_64.AppImage** - Linux portable executable

All are attached to the GitHub Release automatically.

---

## Local Testing

### Test Windows Build Locally
```bash
# Build the Windows app
wails build -platform windows/amd64 -o Plotter.exe

# Build the MSI (requires WiX toolset installed)
wix build -out Plotter-Setup.msi installer.wxs
```

### Test macOS Build Locally (on Mac only)
```bash
# Build universal macOS app
wails build -platform darwin/universal

# Create DMG
brew install create-dmg
create-dmg --volname "Plotter" --window-pos 200 120 --window-size 800 400 --icon-size 100 --icon "Plotter.app" 200 190 --hide-extension "Plotter.app" --app-drop-link 600 190 "Plotter.dmg" "build/bin/"
```

### Test Linux Build Locally (on Linux only)
```bash
# Build the Linux app
wails build -platform linux/amd64

# Create AppImage
mkdir -p AppDir/usr/bin
cp build/bin/Plotter AppDir/usr/bin/
appimage-builder --recipe AppImageBuilder.yml --skip-tests
```

---

## Version Numbering

Update version in these files when releasing:
- `go.mod` comment or hardcode in code
- Tag format: `v1.0.0`, `v1.0.1`, etc.

---

## Distribution

Once GitHub Actions completes:
1. Go to GitHub Releases
2. Download all three installers
3. Update your download page with links to the release
4. Users download the appropriate installer for their OS

---

## Troubleshooting

### Windows MSI Build Fails
- Make sure WiX toolset is installed on your machine
- Verify `build/windows/icon.ico` exists

### macOS Build
- Only works on macOS runners (GitHub Actions uses `macos-latest`)
- Requires Xcode command line tools

### Linux Build
- Requires GTK3 and WebKit2 development libraries
- AppImage requires `appimage-builder` package

---

## Code Signing (Future)

If you get a code signing certificate later:
1. Add certificate to GitHub Secrets
2. Update workflow to sign MSI
3. Enable macOS notarization for DMG
4. Sign AppImage

For now, installers work fine without signing - users just see a warning on first run.
