---
title: "Index"
type: docs
---

## Intro

Vcc - the Vulkan Clang Compiler, is a proof-of-concept C and C++ compiler for Vulkan leveraging Clang as a front-end, and [Shady](https://github.com/shady-gang/shady) our own research IR and compiler. Unlike other shading languages, Vcc aims to stick closely to standard C/C++ languages and merely adds a few new intrinsics to cover GPU features. Vcc is similar to CUDA or Metal in this regard, and aims to bring the advantages of standard host languages to Vulkan shaders[^which_api].

[^which_api]: While Vulkan is the primary target, the architecture of Vcc and Shady is applicable to other target languages and APIs, such as OpenGL/GLSL, or Metal. The former of which actually has some experimental code already present!

## Key Features

Vcc supports advanced C/C++ features usually left out of shading languages such as HLSL or GLSL, in particular raising the bar when it comes to pointer support and control-flow:

 * Unrestricted pointers
   * Arithmetic is legal, they can be bitcasted to and from integers
 * Generic pointers
   * Generic pointers do not have an address space in their type, rather they carry the address space as a tag in the upper bits.
 * True function calls
   * Including recursion, a stack is implemented to handle this in the general case
 * Function pointers
   * Lets you write code in a functional style on the GPU without limitations
 * Arbitrary `goto` statements - code does not need to be strictly structured !

Many of these capabilities are present in compute APIs, but are not supported in most graphics APIs such as DirectX or Vulkan. We aim to address this gap by proving these features can and should be implemented. [More on why we think that's important.](why)

## Status and Caveats

Vcc is still a work-in-progress. While we do commit to making all the previously mentioned features work reliably, and generally attempt to bring the entirety of C and C++ to the GPU without sub-setting, there are still limitations:

 * Certain functions, such as `malloc`/`free` require communicating with the host kernel which we currently do not implement.
 * Non header-only libraries, including parts of the C and C++ standard libraries, will not work on the GPU without additional work, because of the way Vcc works.
   * While addressable, this would massively increase the scope of the project.
   * For now it's best to think of Vcc as only supporting a `-ffreestanding` dialect of C/C++
 * C++ exceptions and their corresponding LLVM instructions (`landingpad` etc) are not supported.
 * Function and data pointers are not portable between host and device. Taking addresses and passing them across in either direction is UB - since Vulkan lacks a unified addressing extension this is a limitation we cannot address at this time.

## Sample code

```c
#include <shady.h>

descriptor_set(0) descriptor_binding(1) uniform_constant sampler2D texSampler;

location(0) input vec3 fragColor;
location(1) input vec2 fragTexCoord;

location(0) output vec4 outColor;

fragment_shader void main() {
    outColor = texture2D(texSampler, fragTexCoord) * (vec4) { fragColor.x * 2.5f, fragColor.y * 2.5f, fragColor.z * 2.5f, 1.0f };
}
```
