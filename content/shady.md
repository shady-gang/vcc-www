---
title: "How this works"
type: docs
---

## What is Shady

Vcc is little more than a front-end for [Shady](https://github.com/Hugobros3/shady), a compiler and IR resulting of years of research and development at Saarland University. Shady was initially started to rationalise our GPU backends by supporting Vulkan in [AnyDSL](https://anydsl.github.io/)

Shady is written in standard C11 in an effort to keep it lightweight, portable and aligned with similar compilers such as NIR. The IR is mostly treated as immutable and passes are designed to _rewrite_ code instead of mutating it.

As a language, it's close to SPIR-V and LLVM but we have our own twist on a few things, in particular control-flow. Shady's representation gives it distinct advantages when emulating constructs not found in SPIR-V. It is by necessity strongly aligned with SPIR-V and uses parts of the format verbatim in the IR, such as the builtins.

You can also think of Shady as implementing a futuristic dialect of SPIR-V that corresponds to our wishlist of features. Shady is free software and we'de love to have other front-ends use it !

## How are pointers dealt with ?

## How do generic pointers work ?

## How do function calls work ?

### Continuation-passing-style (CpS)

### Implementing function pointers

### Optimisations & stack usage

## Using Clang as a front-end without marrying LLVM

To keep project scope in check, we made the decision to use mainline Clang/LLVM and not fork either. This created some additional challenges, but on the flipside means Vcc is not tied to any particular LLVM version, and we don't need to maintain a vendored fork and ship it as a dependency everywhere. In fact you're encouraged to build Vcc with whatever version of Clang and LLVM your operating system ships, where applicable.

The big trick is that Shady just parses LLVM IR, which is be achieved using the LLVM-C interface. The stock Clang driver is invoked by the `vcc` executable and is asked to emit LLVM IR, which Shady then parses and then proceeds to run it's normal pipeline on.