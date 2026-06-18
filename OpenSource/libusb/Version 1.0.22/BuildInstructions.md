# Building libusb 1.0.22 from Sources

> **Windows only.**

Builds as a **static** library, separate Debug and Release configurations.
libusb is unmodified.

## Prerequisites

| Tool | Version |
|---|---|
| libusb sources | 1.0.22: `Sources\` folder, copy to build directory |
| Visual Studio | 2019 (compiler v142 MSVC toolset 14.29.30133 – 64 bits) |

---

## Build

Run from the `msvc\` folder of the copied sources, in a `cmd` prompt after
running `vcvarsall.bat` for VS 2019 x64.

The provided VS 2017 project targets toolset `v141`; it is retargeted to `v142`
on the command line.

```bat
msbuild libusb_static_2017.vcxproj /p:Configuration=Debug /p:Platform=x64 /p:PlatformToolset=v142
msbuild libusb_static_2017.vcxproj /p:Configuration=Release /p:Platform=x64 /p:PlatformToolset=v142
```

## Output

| File | Location (relative to the sources root) |
|---|---|
| Debug library | `x64\Debug\lib\libusb-1.0.lib` |
| Release library | `x64\Release\lib\libusb-1.0.lib` |
| Public header | `libusb\libusb.h` |
