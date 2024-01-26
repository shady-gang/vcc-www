---
title: "Why ?"
type: docs
---

This is a lot of effort. Why ?

## Dissatisfaction with "Legacy" Shading Languages

Back in the early 2000s a significant revolution happened in the world of realtime computer graphics: we moved from "dumb" graphics accelerator that had only a fixed set of functionality (texturing slots, blended vertex colors, hardware T&L ... ) to increasingly programmable hardware. This hardware transition was accompanied by an API transition, de-emphasizing the classic OpenGL state machine in favour of then-new high-level shading languages: GLSL and HLSL.

Since then, comparatively little has happened to the shading language ecosystem for graphics APIs. The aforementioned pair is still relevant for Vulkan and DirectX 12, even though the rest of the API surface has been dramatically modernized. Worse still, their once bleeding-edge capabilities have since then dulled in relevance, and the approach of having dedicated languages with custom syntax directly conflicts with the goals of GPU offloading and heterogenous compute.

### The Compute and Graphics Schism

Broadly speaking, there are two kinds of GPU APIs: compute and graphics. CUDA, ROCm and OpenCL are filed as the former, while Vulkan, DirectX and OpenGL would be the latter.  On the surface it would seem that graphics APIs and compute APIs overlap - both let you program the GPU, and in fact modern graphics APIs offer "compute shaders".

But these compute shaders are not the same as the compute kernels found in dedicated GPGPU APIs, and severely lag behind in terms of features. Modern GPGPUs APIs work towards making GPU programming as close to general programming as possible - implementing complete C and C++ compilers for GPUs that follow normal syntax.

### Working Towards Single-Source GPU programming

Vulkan is arguably not so far behind in capabilities compared to some compute APIs like OpenCL. There are already efforts to layer OpenCL on top of Vulkan and Vcc/Shady are comparable to those, and in fact ahead when it comes to supporting function pointers, since OpenCL does not have them as a standard feature. Generic pointer support in OpenCL is also spotty and optional.

What Vcc shares with OpenCL C, CUDA and even Metal to an extent, is this goal to align host and device code and even unify it. While Vcc is not a true "single source" compiler in the sense that one file can produce both host and device code, it is not only viable, but the intended use-case to have projects compiling the same code twice for host and device without modifications [^vcc_single_source].

[^vcc_single_source]: Making Vcc a "true" single-source framework would involve working with Clang's existing offloading support and is considered out of scope for now.

### The Maintenance Issue

Due to their baggage as C-like languages that were neither proper subsets nor super-sets of C or C++, GLSL and HLSL have had difficulties to adapt to the evolving API landscape. The support for modern features like `VK_KHR_buffer_device_address` is awkward [^awkward], and there is no shortage of bugs and issues plaguing DXC. The maintainers of DXC in fact intend to move to a mainline version of LLVM in the future, and we think this is the right approach, if perhaps not going far enough.

[^awkward]: We believe this is due to their long legacy, where these languages had no pointers in the syntax at all. We get to simply expose the newfound support for pointers in shaders using standard pointer syntax!

In truth, we see little future to bespoke C-like shading languages for GPU programming. A standard Clang toolchain with minor extensions has been shown to be a viable approach for many years in the compute world and on Apple devices, and the benefits both for the users, but also for maintaining the toolchain in the long run, are too large to ignore.

## Bootstrapping the Future

We have been privately arguing for evolving towards a simpler, more expressive future for not only high-level shader code but also the underlying SPIR-V IR capabilities, for giving it access to richer features that align with the programming model of the host.

There is a chicken and egg problem here however, and that is that IHVs are less inclined to spend engineering resources supporting a feature that may require significant internal compiler changes, meanwhile you cannot simply bolt a SPIR-V back-end to LLVM and have that work with Vulkan, due to all the [constraints](https://xol.io/blah/the-trouble-with-spirv/) imposed by Vulkan.

We hope to contribute to solving this chicken and egg problem with [Shady](/how) by removing the bigger limitations that we find stand in the way. Vcc is the result of this work.

