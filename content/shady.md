---
title: "More about Shady"
type: docs
draft: true
---

## What is Shady

Vcc is little more than a front-end for [Shady](https://github.com/Hugobros3/shady), a compiler and IR resulting of years of research and development at Saarland University. Shady was initially started to rationalise our GPU backends by supporting Vulkan in [AnyDSL](https://anydsl.github.io/)

Shady is written in standard C11 in an effort to keep it lightweight, portable and aligned with similar compilers such as NIR. The IR is mostly treated as immutable and passes are designed to _rewrite_ code instead of mutating it.

As a language, it's close to SPIR-V and LLVM but we have our own twist on a few things, in particular control-flow. Shady's representation gives it distinct advantages when emulating constructs not found in SPIR-V. It is by necessity strongly aligned with SPIR-V and uses parts of the format verbatim in the IR, such as the builtins.

You can also think of Shady as implementing a futuristic dialect of SPIR-V that corresponds to our wishlist of features. Shady is free software and we'd love to have other front-ends use it !

## How are pointers dealt with ?

Vulkan offers different "tiers" of support for pointers. The following table summarises those:

| Capability | Usage restrictions or relaxations | Notes |
| --- | --- | --- |
| Logical pointers | Opaque bit-pattterns, cannot be loaded or stored to memory, no pointer arithmetic | Default for Vulkan. Used for resource bindings, may not be implemented as pointers at all[^mem2reg_logical]. |
| Logical + variable pointers | Same as logical but can be loaded/stored to local memory under certain conditions | Adds some limited expressiveness but is ignored by Shady in practice. |
| Logical with reinterpreting | Same as logical but now the same memory can be aliased and reinterpreted as different types. | [SPV_KHR_workgroup_memory_explicit_layout](https://htmlpreview.github.io/?https://github.com/KhronosGroup/SPIRV-Registry/blob/master/extensions/KHR/SPV_KHR_workgroup_memory_explicit_layout.html) implements this for shared memory. |
| Physical pointers | Observable bit-patterns, as powerful as C or LLVM pointers | Default for OpenCL. [SPV_KHR_physical_storage_buffer](https://htmlpreview.github.io/?https://github.com/KhronosGroup/SPIRV-Registry/blob/master/extensions/KHR/SPV_KHR_physical_storage_buffer.html) provides support for physical pointers is available as an optional feature in Vulkan. |

[^mem2reg_logical]: Guarantees pointer values never leak/mem2reg is always legal!

Depending on the address space (storage class), and optional features/extensions Vulkan offers access to different tiers of support for pointters. Shady takes steps to make the most of the available capabilities, but can also resort to emulating physical pointers where required.

This emulation works by basically creating a large array of an integer type (this is configurable, called the "word" size in the compiler) and implementing load and stores to it using helper functions that decompose complex types into words (in the worst case scenario, if aliasing/reinterpreting is not available).

This is however the last resort, and the compiler first performs analysises and optimisations to eliminate as many local allocations as possible, and also determines whether the pointer value is used in a way where logical pointers are sufficient (does not leak and such).

## How do generic pointers work ?

## How do function calls work ?

### Continuation-passing-style (CpS)

### Implementing function pointers

### Optimisations & stack usage

## Using Clang as a front-end without marrying LLVM

To keep project scope in check, we made the decision to use mainline Clang/LLVM and not fork either. This created some additional challenges, but on the flipside means Vcc is not tied to any particular LLVM version, and we don't need to maintain a vendored fork and ship it as a dependency everywhere. In fact you're encouraged to build Vcc with whatever version of Clang and LLVM your operating system ships, where applicable.

The big trick is that Shady just parses LLVM IR, which is be achieved using the LLVM-C interface. The stock Clang driver is invoked by the `vcc` executable and is asked to emit LLVM IR, which Shady then parses and then proceeds to run its normal pipeline on.
