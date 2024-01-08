---
title: "Using Vcc"
type: docs
---

## Getting a copy of Vcc

There currently are no release builds available for Vcc, however it can be compiled as part of Shady which is freely available on [GitHub](https://github.com/Hugobros3/shady). The following dependencies are required:

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

### Required Vulkan version and capabilities

Vcc 