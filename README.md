makefile.lib
============

makefile helper library for basic management of source/target files

Overview
--------

you can have more than one target, just add targets as you wish. you can
share files among targets, files will be compiled for each target with
approtiate flags.

in addition targets may depend each other, so if your target depends on
target.so just add target.so to target's depend list with;

    target_depends-y = target.so

targets may also depend to the subdirectories, so target commands will
not be executed until subdirs commands get executed.

every target and flag variable is also available per platform and per os, so a
target may be built only where it applies;

    target-LINUX        = target.so
    target_cflags-y     = -DUSER_DEFINED
    target_cflags-MACOS = -DUSER_DEFINED_ONLY_FOR_MACOS

some usefull files are created in make process, for debugging and for speedup
 
    .target/*.d                : includes depend information for the file
    .target/*.d.cmd            : the command used for generating .d file
    .target/*.o                : object for the file
    .target/*.o.cmd            : the command used for creating the object
    .target/target[a,so,o]     : the target
    .target/target[a,so,o].cmd : the command used for creating the target
    target                     : the target

Available Suffixes
------------------

    -y                     : always used
    -n, -                  : never used
    -$(TARGET_PLATFORM)    : used when building for that platform
    -$(TARGET_OS)          : used when building for that os

    TARGET_PLATFORM        : WINDOWS, DARWIN, LINUX, UNKNOWN
    TARGET_OS              : WINDOWS, MACOS,  LINUX, UNKNOWN

merged in -y, -$(TARGET_PLATFORM), -$(TARGET_OS) order, each expanded once. so a
value is not repeated when TARGET_PLATFORM and TARGET_OS are equal.

the [y,n, ] below is shorthand, every one of them takes the suffixes above too.

Available Targets
-----------------

    target-[y,n, ]    : a binary target.
                        target will be created with $(CC) -o
    target.o-[y,n, ]  : an object target.
                        target.o will be created with $(LD) -r -o
    target.a-[y,n, ]  : a static library target.
                        target.a will be created with $(AR)
    target.so-[y,n, ] : a shared library target.
                        target.so will be created with $(CC) -o -shared

    target.host-[y,n, ]    : a binary target.
                             target will be created with $(HOSTCC) -o
    target.o.host-[y,n, ]  : an object target.
                             target.o will be created with $(HOSTLD) -r -o
    target.a.host-[y,n, ]  : a static library target.
                             target.a will be created with $(HOSTAR)
    target.so.host-[y,n, ] : a shared library target.
                             target.so will be created with $(HOSTCC) -o -shared

    subdir-[y,n, ] : subdirectory targets are executed with
                     $(subdir-y)_makeflags-y $(MAKE) -C $(subdir-y)

Available Target Flags
----------------------

    $(target)_makeflags-[y,n, ]        : makeflags for target  will be passed to make
                                         command only for corresponding target.
    $(target)_files-[y,n, ]            : files must match *.[cho] pattern. *.[ch] files
                                         will be exemined with $(CC) -M command to
                                         generate dependency files (*.d) files. *.[o]
                                         files will be used only in linking stage. all
                                         files generated with make command will be
                                         removed with $(RM) command.
    $(target)_archflags-[y,n, ]        : archflags will be added to global $(ARCHFLAGS)
                                         for corresponding target only, at both compile
                                         and link stage.
    $(target)_cflags-[y,n, ]           : cflags will be added to global $(CFLAGS) for
                                         corresponding target only.
    $(target)_cxxflags-[y,n, ]         : cxxflags will be added to global $(CXXFLAGS)
                                         for corresponding target only.
    $(target)_mflags-[y,n, ]           : mflags will be added to global $(CFLAGS) for
                                         corresponding target only, for *.m files.
    $(target)_${file}_depends-[y,n, ]  : all words in depends flag will be added to
                                         target file prerequisite's list.
    $(target)_${file}_cflags-[y,n, ]   : cflags will be added to global $(CFLAGS) for
                                         corresponding target file only.
    $(target)_${file}_cxxflags-[y,n, ] : cxxflags will be added to global $(CXXFLAGS)
                                         for corresponding target file only.
    $(target)_${file}_mflags-[y,n, ]   : mflags will be added to global $(CFLAGS) for
                                         corresponding target file only.
    $(target)_includes-[y,n, ]         : a '-I' will be added to all words in includes
                                         flag, and will be used only for corresponding
                                         target.
    $(target)_libraries-[y,n, ]        : a '-L' will be added to all words in libraries
                                         flag, and will be used only for corresponding
                                         target.
    $(target)_ldflags-[y,n, ]          : ldflags will be added to gloabal $(LDFLAGS) for
                                         corresponding target only.
    $(target)_soname-y                 : passed as -Wl,-soname for a target.so.
    $(target)_depends-[y,n, ]          : all words in depends flag will be added to
                                         prerequisite's list.

Distribution Commands
---------------------

    dist.dir                           : distribution folder
    dist.base                          : optional subfolder appended to obj, src, include
                                         and share. empty by default.

    dist.bin-[y,n, ]                   : files to be copied under $(dist.dir)/bin
    dist.lib-[y,n, ]                   : files to be copied under $(dist.dir)/lib
    dist.obj-[y,n, ]                   : files to be copied under $(dist.dir)/obj/$(dist.base)
    dist.src-[y,n, ]                   : files to be copied under $(dist.dir)/src/$(dist.base)
    dist.include-[y,n, ]               : files to be copied under $(dist.dir)/include/$(dist.base)
    dist.share-[y,n, ]                 : files to be copied under $(dist.dir)/share/$(dist.base)

Usage
-----

you can check projects using libmakefile, for detailed information:

  - <a href="https://github.com/anhanguera/libhthread">libhthread</a>, <a href="http://alperakcan.net/projects/libhthread">libhthread</a> 
  - <a href="https://github.com/anhanguera/libhmemory">libhmemory</a>, <a href="http://alperakcan.net/projects/libhmemory">libhmemory</a> 
  - <a href="http://sf.net/projects/xynth">xynth</a>, <a href="http://alperakcan.net/projects/xynth">xynth</a>

Makefile Example using Makefile.lib

    target-y      = target1
    target-y     += target2
    target-LINUX += target3

    target1_files-y = \
        target_file_shared.c \
        target1_file_2.c \
        target1_file_3.c

    target1_includes-y = \
        ./ \
        /opt/include

    target1_libraries-y = \
        ./ \
        /opt/lib

    target1_cflags-y = \
        -DUSER_DEFINED \
        -O2

    target1_cflags-LINUX = \
        -DUSER_DEFINED_ONLY_FOR_LINUX

    target1_cflags-WINDOWS = \
        -DUSER_DEFINED_ONLY_FOR_WINDOWS

    target1_ldflags-y = \
        -luserdefined

    target2_files-y = \
        target_file_shared.c \
        target2_file_2.c \
        target2_file_3.c

    target2_files-WINDOWS = \
        target2_file_windows.c

    target2_includes-y = \
        ./ \
        /opt/include

    target2_libraries-y = \
        ./ \
        /opt/lib

    target2_cflags-y = \
        -DUSER_DEFINED \
        -O2

    target2_target2_file_2.c_cflags-y = \
        -DUSER_DEFINED_ONLY_FOR_TARGET2_FILE_2_C

    target2_ldflags-y = \
        -luserdefined

    target3_files-y = \
        target3_file_1.c

    include Makefile.lib
