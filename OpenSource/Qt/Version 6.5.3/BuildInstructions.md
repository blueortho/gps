# Building Qt 6.5.3 from Sources

## Prerequisites

### Common (all platforms)

| Tool | Version |
|---|---|
| Qt sources | 6.5.3: `Sources\` folder, copy to build directory |
| OpenSSL | 3.0.13 (pre-built) |
| zlib | 1.3.1 (pre-built) |
| SQLCipher | 4.5.6 — build first to produce `sqlite3.c` / `sqlite3.h`, then copy them to `qtbase/src/3rdparty/sqlite/` in the copied Qt sources |
| Python | ≥ 3.7 |
| CMake | latest stable |
| Ninja | latest stable |

### Windows

| Tool | Version |
|---|---|
| Visual Studio | 2019 (compiler v142 MSVC toolset 14.29.30133 – 64 bits)  |

### macOS

| Tool | Version |
|---|---|
| Xcode | 15.3 |

> **Note:** On Windows, keep the project path as short as possible (e.g. a directory near the filesystem root) to avoid build failures due to path-length restrictions.

---

## Build

### 1. Apply the patch

Apply `Modification.patch` to the copied Qt sources (sqlite & sqldrivers plugin, platforms plugin, qtmultimedia).

### 2. Configure

Run from the copied source directory. Adjust the paths in angle brackets to your environment.

**Windows (`cmd`, after running `vcvarsall.bat` for VS 2019 x64):**

```bat
configure.bat ^
  -opensource -confirm-license ^
  -debug-and-release -force-debug-info -separate-debug-info ^
  -shared ^
  -qt-libjpeg -no-icu ^
  -prefix <INSTALL_DIR> ^
  -nomake tests ^
  -DQT_MESSAGELOGCONTEXT ^
  -skip qtcharts,qtdatavis3d,qtlottie,qtmqtt,qtnetworkauth,qtquick3d,qtquick3dphysics,qtquicktimeline,qtvirtualkeyboard,qtwayland,qtwebengine ^
  -platform win32-msvc ^
  -opengl desktop ^
  -qt-sqlite -plugin-sql-sqlite ^
  -openssl-linked ^
  -I <OPENSSL_3.0.13_DIR>/include -L <OPENSSL_3.0.13_DIR>/lib ^
  -system-zlib ^
  -I <ZLIB_1.3.1_DIR>/include -L <ZLIB_1.3.1_DIR>/lib ^
  -- ^
  -DOPENSSL_ROOT_DIR=<OPENSSL_3.0.13_DIR> ^
  -DZLIB_ROOT=<ZLIB_1.3.1_DIR> ^
  -DZLIB_USE_STATIC_LIBS=ON ^
  -DZLIB_INCLUDE_DIR=<ZLIB_1.3.1_DIR>/include ^
  -DBUILD_WITH_PCH=NO
```

**macOS:**

```sh
./configure \
  -opensource -confirm-license \
  -debug-and-release -force-debug-info \
  -shared \
  -qt-libjpeg -qt-libpng -qt-webp -qt-tiff -no-icu -no-jasper \
  -prefix <INSTALL_DIR> \
  -nomake tests \
  -DQT_MESSAGELOGCONTEXT \
  -skip qtcharts,qtdatavis3d,qtlottie,qtmqtt,qtnetworkauth,qtquick3d,qtquick3dphysics,qtquicktimeline,qtvirtualkeyboard,qtwayland,qtwebengine \
  -platform macx-clang \
  -opengl desktop \
  -qt-sqlite -plugin-sql-sqlite \
  -openssl-linked \
  -I <OPENSSL_3.0.13_DIR>/include -L <OPENSSL_3.0.13_DIR>/lib \
  -system-zlib \
  -I <ZLIB_1.3.1_DIR>/include -L <ZLIB_1.3.1_DIR>/lib \
  -- \
  -DOPENSSL_ROOT_DIR=<OPENSSL_3.0.13_DIR> \
  -DZLIB_ROOT=<ZLIB_1.3.1_DIR> \
  -DZLIB_USE_STATIC_LIBS=ON \
  -DZLIB_INCLUDE_DIR=<ZLIB_1.3.1_DIR>/include \
  -DCMAKE_OSX_ARCHITECTURES="arm64;x86_64" \
  -DCMAKE_OSX_DEPLOYMENT_TARGET=11.0
```

### 3. Build and install

```
ninja install
```
