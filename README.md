# RHLib for OMSI 2

A "library" of script components, reducing duplication of code and of execution.

## Disclaimer

- This is **not** a bus mod, this is a resource for developers to add to their
  existing or in-development buses.
- I cannot be held personally responsible if the inclusion of these scripts
  directly or indirectly results in the breaking of your OMSI/OMSI 2
  installation or bus; or loss of data.

## Redistribution

This library is licensed under Version 2.0 of the Apache License, with the full
details of the license available in either LICENSE.txt or
[online](http://www.apache.org/licenses/LICENSE-2.0).

- This permits use in both open and closed source, and both freeware and payware
  projects.
- Modifying the library is also permitted, however you must retain the copyright
  notice and license boiler plate at the top of the files, as well as stating
  what changes have been made.

## Usage Information

The library is divided into several modules such that only the required
components need be loaded or run, improving performance.

Remember that OMSI Script requires macros to be called first and defined later,
so the RHLib script files should generally come last under `[script]`. Equally,
the calls to RHLib init and frame macros should occur early, so that variables
are set before any scripts try to access them - this also applies to RHLib
modules that access variables from another module.

### String Registers

To use this module, `rhlib_register_stringvarlist.txt` must be listed under
`[stringvarnamelist]`.

This module provides string variables `temp0` to `temp9`, to be used as string
equivalents to the 10 float registers (`l0`-`l9`/`s0`-`s9`).

### Infinity Constant

To use this module, `rhlib_infinity_constfile.txt` must be listed under
`[constfile]`.

In OMSI Script dividing by 0 evaluates to 0 rather than infinity, undefined or
an exception. This constant can be used in such situations where a value of
infinity is desirable. This is achieved by trying to store a value of 4.0×10³⁸
into a 32-bit float constant that can only handle up to ~3.4×10³⁸.
