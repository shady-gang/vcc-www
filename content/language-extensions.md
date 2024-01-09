---
title: "C/C++ Language Extensions"
type: docs
---

## Using <shady.h>

The `<shady.h>` header includes definitions for intrinsics and macros that are unique to shaders and not exposed in the C/C++ standard librairies.

It is _not_ necessary to include this file in every translation unit, only in the files where you want to use those features, and it is also required to annotate shader module entry points.

### Vector types

Vcc makes uses of the existing OpenCL C support in Clang and exposes small `vec`-types as also found in GLSL. These are typedef'd as such:

```c
typedef float vec4     __attribute__((ext_vector_type(4)));
typedef float vec3     __attribute__((ext_vector_type(3)));
typedef float vec2     __attribute__((ext_vector_type(2)));

typedef int ivec4      __attribute__((ext_vector_type(4)));
typedef int ivec3      __attribute__((ext_vector_type(3)));
typedef int ivec2      __attribute__((ext_vector_type(2)));

typedef unsigned uvec4 __attribute__((ext_vector_type(4)));
typedef unsigned uvec3 __attribute__((ext_vector_type(3)));
typedef unsigned uvec2 __attribute__((ext_vector_type(2)));
```

Component swizzling on those types is supported.

### Entry points

Shader entry points need to be annotated with one of:

 * `fragment_shader`
 * `vertex_shader`
 * `compute_shader`
   * Also requires setting `local_size(x, y, z)`

### Resources / Pipeline layout stuff

Vulkan shaders have to provide some extra information when declaring certain kinds of external variables, that are bound by the host/API side.

#### Descriptor set/binding annotations

UBOs, SSBOs and texturing resources need to be annotatted with `descriptor_binding()` and `descriptor_set()`:

```c
descriptor_set(0) descriptor_binding(1)
uniform int my_funny_buffer[256];
```

#### Shader input / outputs

Similar to recent versions of GLSL, `input` and `output` variables need to provide a `location(N)`:

```c
location(0)
input vec2 texCoord;

location(0)
output vec4 pixelColor;
```

#### Push constants

### Address spaces

LLVM and Clang already support address spaces using the `__attribute__((address_space(N)))` syntax. Shady merely reserves some numbers for custom address spaces, and tries to line up with other targets where possible.

TODO: table

While Shady supports generic pointers, only `private`, `shared` and `global` will be able to work with them. There are many more address spaces found in SPIR-V that have usage restrictions that make them pointless to support in generic pointers, and therefore those need to be explicitly specified. This is also true for global variables.

### Texturing
