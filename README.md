# Warning: This is old code

SILK has been superseded by [Opus](http://opus-codec.org/). Please see [Skype's blog post](http://blogs.skype.com/2012/09/12/skype-and-a-new-audio-codec/).

# SILK codec for ARM/iOS

This is a modified version of the SILK 1.0.8 codec with optimized ARM/NEON assembly. Compiling and
running SILK turned out to be quite a hassle, so I decided to distribute the results of my work here.
Note that this is really just a quick&dirty thing, I will not be able to provide any kind of support
or help about this.

## 64 bit ARM compatibilty

This code does not compile as 64 bit ARM, at least not the optimized assembly version. I haven't
investigated why, but I believe it's simply because there is no 64 bit ARM assembly implementation.
There will probably never be one either; SILK has been superseded by [Opus](http://opus-codec.org/). Please see [Skype's blog post](http://blogs.skype.com/2012/09/12/skype-and-a-new-audio-codec/)..

## Patent notice

For more information regarding patents, see [Skype's SILK website](https://developer.skype.com/silk/)

## How to use

The easiest way to use this code is to simply add all source files to the Xcode project. It should
compile and run happily. It is also possible to create a static library file using the Makefile,
but that's more of a hassle, and will probably not work out of the box.

##  What I did to get it to compile and run

Here are a couple of the things I found out in the process
of trying to compile SILK:

http://gcc.gnu.org/ml/gcc-patches/2005-08/msg00565.html
For complaints about a missing __aeabi_idiv, it can be
replaced with __divsi3. __aeabi_idiv comes from libgcc,
and can't be used when using Clang.

For complaints about that arguments ought to be registries,
that's a macro issue. Manually expanding those macros solves
those errors.

The Makefile has to be edited rather heavily to work.
The main things that have to be done are to replace $CC,
$CXX, $AR and $RANLIB to be like this:

    CC     = `xcrun -sdk ${IPHONE_SDK} -find clang`
    [etc]

where

    IPHONE_SDK = /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/SDKs/iPhoneOS5.1.sdk

I also added this:

    CFLAGS += -arch armv7 -mthumb -isysroot ${IPHONE_SDK}
    LDFLAGS += -arch armv7 -mthumb -isysroot ${IPHONE_SDK}
    USE_NEON=yes

In addition to that, I had to short-circuit the if clause
that looks like this:

    ifneq (,$(TOOLCHAIN_PREFIX))

Otherwise the assembly files wouldn't get compiled.

There were also 4 byte align warnings from ld about the assembly-defined
functions. At first, I ignored them, but then I got illegal instruction
errors. These were fixed by adding a ".align 2" line before the ".globl"
line.

