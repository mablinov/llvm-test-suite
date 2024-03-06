include(${CMAKE_CURRENT_SOURCE_DIR}/../../DgOptions.txt)

set(TestsToSkip)

##
## Main Test Blacklist for Clang
##

# Tests with features unsupported by Clang (usually GCC extensions)
# (Big list of naughty tests)
file(GLOB UnsupportedTests CONFIGURE_DEPENDS
  # Sketchy builtin vector ops, e.g. attempting to index into builtin
  # packed vector arrays
  forwprop-19.c
  forwprop-20.c
  forwprop-21.c
  forwprop-23.c
  vector-4.c

  # clang: error: invalid value 'IBM1047' in '-fexec-charset=IBM1047'
  builtin-sprintf-11.c
  builtin-sprintf-warn-18.c

  # error: builtin functions must be directly called
  builtin-printf-warn-1.c

  builtin-fprintf-warn-1.c

  builtin-sprintf-warn-1.c
  builtin-sprintf-warn-9.c
  builtin-sprintf-warn-11.c
  builtin-sprintf-warn-12.c
  builtin-sprintf-warn-13.c
  builtin-sprintf-warn-14.c
  builtin-sprintf-warn-15.c

  builtin-snprintf-warn-3.c

  ssa-ccp-20.c

  # GCC-specific `__GIMPLE` attribute
  cunroll-13.c
  phi-opt-26.c
  pr80887.c
  copy-headers-3.c
  ivopt_mult_1g.c
  ivopt_mult_2g.c
  pr98513.c
  ranger-threader-5.c
  ssa-ccp-42.c
  ssa-dom-cse-8.c
  ssa-fre-70.c
  ssa-fre-69.c
  ssa-fre-71.c
  ssa-fre-72.c
  ssa-fre-79.c
  ssa-fre-75.c
  ssa-lim-13.c
  ssa-thread-valid.c
  ssa-thread-invalid.c
  vrp-float-relations-4.c
  vrp-float-relations-5.c
  vrp-float-relations-6.c
  

  # Invalid `main` declaration
  loadpre2.c
  loadpre4.c
  loadpre18.c
  loadpre21.c
  loadpre22.c
  pr59597.c
  reassoc-3.c
  reassoc-5.c
  reassoc-6.c
  reassoc-8.c
  reassoc-9.c
  reassoc-10.c
  reassoc-11.c
  
  # I don't think Clang allows (or even parses) nested function definitions
  modref-12.c
  modref-dse-6.c
  pr101824.c
  tailcall-7.c
  tailcall-7-run.c
  
  # Needs LLVM Gold plugin
  pr71077.c

  # 'variable length array in structure' extension will never be supported
  loop-interchange-15.c

  # uses dummy mnemonics within `asm` stmts, which GCC allow but Clang doesnt 
  pr108684-1.c
  pr71947-4.c
  pr71947-5.c
  pr49642-1.c
  pr49642-2.c
  pr38699.c
  ssa-copyprop-2.c
  
  # call to '____' declared with 'error' attribute:
  forwprop-29.c

  # "cannot compile this builtin function yet"
  ssa-dse-30.c

  # These tests are intended to fail linking to excercise parts of GCC
  # that aren't relevant to us, so turn off for now.
  ssa-fre-61.c
  vrp63.c
  vrp65.c
  vrp76.c
  vrp78.c
  vrp80.c

  # Misc
  builtin-sprintf.c
  ssa-dse-10.c

  # Test case that takes ages to build
  pr93435.c
  pr43491.c
  builtins-folding-gimple-ub.c

  # Test only targets archs will `sizeof(int) < 4`:
  vrp66-int16-sw.c

  # Test dependent on fragile `__builtin_constant_p` assertion
  modref-2.c
)

list(APPEND TestsToSkip ${UnsupportedTests})

# Tests where clang currently has bugs or issues
file(GLOB FailingTests CONFIGURE_DEPENDS
  dummy
)
list(APPEND TestsToSkip ${FailingTests})

# Tests that require a particular C/C++ standard

# RISC-V Test Blacklist
if(ARCH MATCHES "riscv")
  file(GLOB RISCVTestsToSkip CONFIGURE_DEPENDS
    dummy
  )

  if (LIBC MATCHES "musl")
    file(GLOB TestsToSkipForMuslLibc
      dummy
    )
    list(APPEND TestsToSkip ${TestsToSkipForMuslLibc})
  endif()

  if (ARCH MATCHES "riscv32")
    file(GLOB RISCV32TestsToSkip CONFIGURE_DEPENDS
      dummy
    )

    list(APPEND RISCVTestsToSkip ${RISCV32TestsToSkip})
  endif()

  list(APPEND TestsToSkip ${RISCVTestsToSkip})
endif()

# x86-only Tests
if(NOT ARCH MATCHES "x86")
  file(GLOB X86OnlyTests CONFIGURE_DEPENDS
    dummy
  )

  list(APPEND TestsToSkip ${X86OnlyTests})
endif()

# arm-only Tests
if(NOT ARCH MATCHES "arm")
  file(GLOB armOnlyTests CONFIGURE_DEPENDS
    dummy
  )

  list(APPEND TestsToSkip ${armOnlyTests})
endif()

# aarch64-only Tests
if(NOT ARCH MATCHES "aarch64")
  file(GLOB aarch64OnlyTests CONFIGURE_DEPENDS
    dummy
  )

  list(APPEND TestsToSkip ${aarch64OnlyTests})
endif()

# mips-only Tests
if(NOT ARCH MATCHES "mips")
  file(GLOB mipsOnlyTests CONFIGURE_DEPENDS
    dummy
  )

  list(APPEND TestsToSkip ${mipsOnlyTests})
endif()

# powerpc-only Tests
if(NOT ARCH MATCHES "powerpc")
  file(GLOB POWERPCOnlyTests CONFIGURE_DEPENDS
    dummy
  )

  list(APPEND TestsToSkip ${POWERPCOnlyTests})
endif()

# mmix-only Tests
if(NOT ARCH MATCHES "mmix")
  file(GLOB mmixOnlyTests CONFIGURE_DEPENDS
    dummy
  )

  list(APPEND TestsToSkip ${mmixOnlyTests})
endif()

# Skip the test cases non-supported for non-linux targets
if (NOT CMAKE_SYSTEM_NAME MATCHES "Linux")
  file(GLOB TestsToSkipForNonLinux
    dummy
  )
  list(APPEND TestsToSkip ${TestsToSkipForNonLinux})
endif()

if (CMAKE_SYSTEM_NAME MATCHES "Linux")
  file(GLOB TestsToSkipForLinux
    dummy
  )
  list(APPEND TestsToSkip ${TestsToSkipForLinux})
endif()

if(FEATURE_ATOMIC MATCHES "OFF")
  file(GLOB TestsToSkipForNonAtomic
    dummy
  )
  list(APPEND TestsToSkip ${TestsToSkipForNonAtomic})
endif()

if (NOT MARCH MATCHES "f")
  file (GLOB TestsToSkipForNonFPU
    dummy
  )
  list(APPEND TestsToSkip ${TestsToSkipForNonFPU})
endif()

##
## Tests that require extra CFLAGS in Clang
##

# Tests that require libm (-lm ldflag)
file(GLOB TestRequiresLibM CONFIGURE_DEPENDS
  dummy
)

# Tests that require libatomic (-latomic ldflag)
file(GLOB TestRequiresLibAtomic CONFIGURE_DEPENDS
  dummy
)

# Tests that require '__declspec' attributes
file(GLOB TestRequiresDECLSPEC CONFIGURE_DEPENDS
  dummy
)

if (CMAKE_C_COMPILER_ID MATCHES "GNU")
  list(APPEND TestsToSkip ${TestRequiresDECLSPEC})
endif()

# Skip time consumed tests
if(TEST_SUITE_TEST_MODE MATCHES "fast-run")
  file(GLOB TimeConsumingTests CONFIGURE_DEPENDS
    dummy
  )

  list(APPEND TestsToSkip ${TimeConsumingTests})
endif()

# Skip fixed opt level tests when there are extra cflags
if(EXTRA_CFLAGS MATCHES "")
  file(GLOB OptFixedTests CONFIGURE_DEPENDS
    dummy
  )

  list(APPEND TestsToSkip ${OptFixedTests})
endif()

# Tests that require -Wno-return-type
file(GLOB TestRequiresWNoReturnType CONFIGURE_DEPENDS
  20030729-1.c
  vrp27.c
)

# Tests that require -ffixed-point
file (GLOB TestRequiresFFixedPoint CONFIGURE_DEPENDS
  dummy
)

# Tests that use varargs on segmented stacks, and are not supported by
# the platform
if(ARCH MATCHES "x86" OR ARCH MATCHES "arm")
  file(GLOB UnsupportedTests CONFIGURE_DEPENDS
    dummy
  )
endif()

list(APPEND TestsToSkip ${UnsupportedTests})

##
## Test target setup
##

file(GLOB TestFiles CONFIGURE_DEPENDS
  *.c
)
foreach(TestToSkip ${TestsToSkip})
  list(REMOVE_ITEM TestFiles ${TestToSkip})
endforeach()

foreach(File ${TestFiles})
  set(MaybeCFlags)
  set(MaybeLDFlags)

  # Add Test-specific CFLAGS/LDFLAGS here

  if(${File} IN_LIST TestRequiresLibM)
    list(APPEND MaybeLDFlags "-lm")
  endif()

  if(${File} IN_LIST TestRequiresLibAtomic)
    list(APPEND MaybeLDFlags "-latomic")
  endif()

  if(${File} IN_LIST TestRequiresDECLSPEC)
    list(APPEND MaybeCFlags "-fdeclspec")
  endif()

  if(${File} IN_LIST TestRequiresWNoReturnType)
    list(APPEND MaybeCFlags "-Wno-return-type")
  endif()

  if(${File} IN_LIST TestRequiresFFixedPoint)
    list(APPEND MaybeCFlags "-ffixed-point")
  endif()

  # Add Test Target
  gcc_torture_execute_test(${File}
                           PREFIX "GCC-DG"
                           CFLAGS ${MaybeCFlags}
                           LDFLAGS ${MaybeLDFlags})

endforeach()

