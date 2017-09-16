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