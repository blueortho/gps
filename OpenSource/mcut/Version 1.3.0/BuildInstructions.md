# Building MCUT 1.3.0 from Sources

MCUT is modified (see `Modification.patch`) and is built as a **shared**
library so that the version linked into the product can be replaced by the
user, as required by the LGPL v3+.

## Prerequisites

### Common (all platforms)

| Tool | Version |
|---|---|
| MCUT sources | 1.3.0: `Sources\` folder, copy to build directory |
| MCUT patch | `Modification.patch` |
| CMake | latest stable |
| Python | 3 or later (only for the automated build script) |

MCUT has no external dependencies. Its only third-party component (CDT) is
vendored under `include/mcut/internal/cdt/` and builds with the library.

### Windows

| Tool | Version |
|---|---|
| Visual Studio | 2019 Professional (compiler v142 MSVC toolset – 64 bits) |
| Inno Setup | 6 (packaging only) |

### macOS

| Tool | Version |
|---|---|
| Xcode | 11.2 or later |

---

## Build

Both configurations (`Debug` and `RelWithDebInfo`) are built and installed.

### 1. Apply the patch

Apply `Modification.patch` to the copied MCUT sources:

```
git apply --ignore-space-change --ignore-whitespace <PATCH_DIR>/Modification.patch
```

It adds the `test_manifold()` mesh-validity API (`source/validity.cpp`,
`include/mcut/validity.h`), registers it in `CMakeLists.txt`, and comments out
the unused `compress()` function in `source/shewchuk.c`.

### 2. Configure

Run once per configuration (`<CONFIG>` = `Debug`, then `RelWithDebInfo`).
Adjust the paths in angle brackets to your environment.

**Windows (`cmd`, after running `vcvarsall.bat` for VS 2019 x64):**

```bat
cmake -S <SOURCE_DIR> -B <BUILD_DIR> ^
  -G "Visual Studio 16 2019" ^
  -DCMAKE_BUILD_TYPE=<CONFIG> ^
  -DCMAKE_INSTALL_PREFIX=<INSTALL_DIR> ^
  -DMCUT_BUILD_AS_SHARED_LIB=True ^
  -DMCUT_BUILD_TESTS=False ^
  -DMCUT_BUILD_TUTORIALS=False ^
  -DMCUT_BUILD_WITH_COMPUTE_HELPER_THREADPOOL=False ^
  -DCMAKE_MSVC_RUNTIME_LIBRARY=MultiThreadedDLL
```

**macOS:**

```sh
cmake -S <SOURCE_DIR> -B <BUILD_DIR> \
  -G Xcode \
  -DCMAKE_BUILD_TYPE=<CONFIG> \
  -DCMAKE_INSTALL_PREFIX=<INSTALL_DIR> \
  -DMCUT_BUILD_AS_SHARED_LIB=True \
  -DMCUT_BUILD_TESTS=False \
  -DMCUT_BUILD_TUTORIALS=False \
  -DMCUT_BUILD_WITH_COMPUTE_HELPER_THREADPOOL=False \
  -DCMAKE_OSX_ARCHITECTURES="arm64;x86_64" \
  -DCMAKE_OSX_DEPLOYMENT_TARGET=11.0
```

> `MCUT_BUILD_AS_SHARED_LIB=True` is the upstream default and **must not be set
> to False**. A static build would replace the simple dynamic-linking route to
> LGPL compliance with the far heavier obligation of shipping object code
> sufficient to relink the application.

### 3. Build and install

```
cmake --build <BUILD_DIR> --config <CONFIG>
cmake --install <BUILD_DIR> --config <CONFIG>
```

### Output

Debug artefacts carry a `d` suffix (`CMAKE_DEBUG_POSTFIX`).

| Platform | Release | Debug |
|---|---|---|
| Windows | `mcut.dll` + import `mcut.lib` (+ `.pdb`) | `mcutd.dll` + `mcutd.lib` (+ `.pdb`) |
| macOS | `libmcut.dylib` | `libmcutd.dylib` |

plus the public headers `mcut.h`, `platform.h` and `validity.h` under
`include/mcut/`.

