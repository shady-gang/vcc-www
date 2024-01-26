---
title: "How this works"
type: docs
---

## What is Shady

Vcc is little more than a front-end for [Shady](https://github.com/shady-gang/shady), a compiler and IR resulting from years of research and development at Saarland University. Shady was initially started to rationalize our GPU backends by supporting Vulkan in [AnyDSL](https://anydsl.github.io/)

As a language, it's close to SPIR-V and LLVM but we have our own twist on a few things, in particular control-flow. Shady's representation gives it distinct advantages when emulating constructs not found in SPIR-V. It is by necessity strongly aligned with SPIR-V and uses parts of the format verbatim in the IR, such as the builtins.

You can think of Shady as implementing a futuristic dialect of SPIR-V that corresponds to our wishlist of features. We are currently working towards publishing papers on the internals of shady and we will be updating this website with more detail once we do.

Shady is free software and we'd love to have other front-ends use it ! This is an [explicit goal](/why/) of the project.

## Relationship to Clang and LLVM

Vcc is a wrapper for the `clang` driver and directs it to emit the LLVM IR for your source files. LLVM is not involved in any optimizations on the code, instead Shady parses the LLVM IR and runs its set of optimization and legalization passes to turn the IR into something that can run on Vulkan. Finally, Shady emits SPIR-V code that your application can then use.

Vcc does _not_ use a fork of LLVM nor Clang. It needs to link against LLVM to use its bitcode parser and C API, but any recent version of LLVM should work. Shady supports both typed and untyped pointers so a large range of LLVM versions should work with it.

## Exposing SPIR-V/Vulkan as C/C++ language extensions

Since we are not modifying Clang, we had to find a way to add our own intrinsics to expose features not present in standard C/C++, such as fixed-function texturing support. This is done through the general-purpose `__attribute__((annotate("")))` annotations, which are thankfully retained in the IR. Shady comes with a `<shady.h>` header that you can simply include in your application to use the extra features. You can look at the Vcc [tests](https://github.com/shady-gang/shady/tree/master/test/vcc) and [samples](https://github.com/shady-gang/shady/tree/master/samples) for guidance on how to use these.

Some features are currently not exposed, but that doesn't mean that Vcc/Shady cannot easily support them. As we are developing this we are constantly exposing new extensions, sometimes it is just a matter of adding appropriate intrinsics. Feel free to raise issues on GitHub if there is something you'd like to see.
