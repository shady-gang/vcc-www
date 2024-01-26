---
title: "Using Vcc"
type: docs
---

## Getting a copy of Vcc

There currently are no release builds available for Vcc, however it can be compiled as part of Shady which is freely available on [GitHub](https://github.com/shady-gang/shady). The following dependencies are required:

 * A C11 compliant compiler
 * CMake 3.13 or later
 * json-c
 * SPIRV-Headers
 * Vulkan-Headers are optional but are used to build some components of shady such as tests
 * A copy of LLVM that includes the CMakeConfig.cmake files, so that Vcc can link against it
   * Multiple LLVM versions are supported because we use LLVM-C, which has a more stable interface. Versions 14 to 17 have been tested so far.

Additionally, a copy of `clang` on your path is required at runtime for `vcc` to operate. This copy _may_ be a different version than the LLVM you linked Vcc against, however might cause issues due to how the LLVM IR evolves over time.

## Using Vcc

Vcc is used much like GCC or Clang is. In fact, most arguments are just forwarded to Clang! Using `vcc --help` one can a list of vcc specific arguments and flags. Vcc can produce SPIR-V binary files, and also has experimental support for GLSL and ISPC output. These SPIR-V files can then be ingested by your application as normal.

### Language extensions and <shady.h>

See [C/C++ Language Extensions](/language-extensions).

### Required Vulkan version and capabilities

Vcc currently requires Vulkan 1.1 and support for `VK_KHR_buffer_device_address`. It also currently requires support for `Int8`, `Int16` and `Int64` and subgroup ballots. These features are ubiquitous on desktop-class hardware and even many phones nowadays.

Some features are or will be made optional, with some corresponding degradations on the capabilities of the compiler. We are still working on organizing the compiler flags and we will be writing up a more comprehensive support matrix later.

### Supported drivers

The following drivers and hardware have been tested with Shady:

| Name | Result | Notes |
| ---- | ------ | ----- |
| `radv` (AMD Mesa) | OK | Main development target |
| `amdvlk` (AMD open-source) | OK | |
| AMD proprietary (Windows) | OK | |
| NVidia proprietary (Windows) | OK | Has/had some small driver issues, with workarounds. |
| `nvk` | Mixed, needs more testing | Subgroup support for Maxwell is still WIP. |
| `anv` (Intel Mesa) | OK | |
| Intel Arc proprietary (Windows) | OK | Older drivers lack Int64, make sure to update |
| MoltenVK | Mixed | SPIRV-Cross does not like Shady's codegen. Patches WIP. |
| Imagination proprietary (BXT series) | Needs more testing | Tested on VisionFive II, small examples work | 
| `llvmpipe` (Mesa software rendering) | KO | Lacks extension support, has weird SIMT behavior |
| SwiftShader (Google software rendering) | KO | Lacks extension support, needs more testing |
