# Building Python 3.12 Binary Installers Locally (macOS & Windows)

This guide provides step-by-step instructions to build binary installers for Python 3.12 (matching the types distributed at [python.org for 3.12.10](https://www.python.org/downloads/release/python-31210/)) on both macOS and Windows.

## Minimum Version & Rationale

Starting with Python 3.12.11, official binary installers for macOS and Windows are no longer provided by the Python release team. Only source distributions and security updates are published for these versions. Therefore, you must build your own binary installers for Python 3.12.11 and later if you require them for local installation or distribution.

Earlier Python 3.12 releases (up to 3.12.10) included official binary installers, so manual builds are only necessary for 3.12.11 and newer.

## Prerequisites

- **Source Code:** Download the official Python 3.12.x source from [python.org](https://www.python.org/ftp/python/3.12.10/Python-3.12.10.tgz).
- **Build Tools:**
  - macOS: Xcode, Homebrew, required libraries
  - Windows: Visual Studio, required SDKs
- **Packaging Tools:**
  - macOS: `pkgbuild`, `productbuild`, `create-dmg`
  - Windows: `msi` tools (WiX Toolset)

---

## macOS Installer (.pkg and .dmg)

### 1. Install Dependencies
```sh
xcode-select --install
brew install openssl readline sqlite3 xz zlib
```

### 2. Prepare Source
```sh
tar -xzf Python-3.12.10.tgz
cd Python-3.12.10
```

### 3. Configure Build
```sh
./configure \
  --enable-optimizations \
  --with-openssl=$(brew --prefix openssl) \
  --prefix=/Library/Frameworks/Python.framework/Versions/3.12
```

### 4. Build & Install
```sh
make -j$(sysctl -n hw.ncpu)
sudo make altinstall
```

### 5. Create .pkg Installer
```sh
sudo pkgbuild \
  --root /Library/Frameworks/Python.framework/Versions/3.12 \
  --identifier org.python.Python \
  --version 3.12.10 \
  Python-3.12.10.pkg
```

### 6. Create .dmg Disk Image (Optional)
```sh
brew install create-dmg
create-dmg Python-3.12.10.pkg
```

---

## Windows Installer (.exe and .msi)

### 1. Install Dependencies
- [Visual Studio 2022](https://visualstudio.microsoft.com/) (Desktop development with C++)
- [WiX Toolset](https://wixtoolset.org/)
- [NASM](https://www.nasm.us/) (for OpenSSL)

### 2. Prepare Source
- Extract Python source
- Open `PCbuild\pcbuild.sln` in Visual Studio

### 3. Build Python
- Select `Release` configuration
- Build the solution (F7)

### 4. Build MSI Installer
- Run `Tools\msi\msi.py` (from a Developer Command Prompt):
```cmd
cd Tools\msi
python msi.py
```
- This generates `.msi` installers in `PCbuild\amd64` and `PCbuild\win32`

### 5. Build .exe Installer (Optional)
- Use WiX Toolset or Inno Setup to wrap the MSI into an .exe installer

---

## Notes
- For both platforms, ensure you match the build options and dependencies used by the official Python releases for compatibility.
- Refer to the [Python Developer Guide](https://devguide.python.org/) and [official build instructions](https://github.com/python/cpython/blob/main/README.rst) for advanced options and troubleshooting.

---

## References
- [Python 3.12.10 Release Files](https://www.python.org/downloads/release/python-31210/)
- [Python Build Instructions (macOS)](https://devguide.python.org/getting-started/setup-building/#macos)
- [Python Build Instructions (Windows)](https://devguide.python.org/getting-started/setup-building/#windows)
- [WiX Toolset Documentation](https://wixtoolset.org/documentation/)

---

*This guide helps you reproduce the official Python binary installers for local distribution or testing.*
