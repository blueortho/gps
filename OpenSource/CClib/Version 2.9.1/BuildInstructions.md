# Building CCLib 2.9.1 from Sources

> **Windows only.**

## Prerequisites

| Tool | Version |
|---|---|
| CCLib sources | 2.9.1: `Sources\` folder, copy to build directory |
| Visual Studio | 2019 (compiler v142 MSVC toolset 14.29.30133 – 64 bits) |
| CMake | latest stable |

---

## Build

### 1. Configure

Run from the `CC\` folder of the copied sources. Adjust the paths in angle brackets to your environment.

**Windows (`cmd`, after running `vcvarsall.bat` for VS 2019 x64):**

```bat
cmake -S <SOURCE_DIR>\CC -B <BUILD_DIR> ^
  -DCMAKE_INSTALL_PREFIX=<INSTALL_DIR> ^
  -DCOMPILE_CC_CORE_LIB_WITH_QT=OFF ^
  -DCOMPILE_CC_CORE_LIB_WITH_CGAL=OFF ^
  -DCOMPILE_CC_CORE_LIB_SHARED=ON ^
  -DCMAKE_CXX_FLAGS="/DNOMINMAX /D_CRT_SECURE_NO_WARNINGS /D__STDC_LIMIT_MACROS" ^
  -DCMAKE_CXX_FLAGS_RELEASE="/D_SECURE_SCL=0"
```

### 2. Build

```bat
cmake --build <BUILD_DIR> --config Debug
cmake --build <BUILD_DIR> --config Release
```

### 3. Install

```bat
cmake --install <BUILD_DIR> --config Debug
cmake --install <BUILD_DIR> --config Release
```
