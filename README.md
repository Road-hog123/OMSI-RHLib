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

### Core

To use this module:

- `rhlib_varlist.txt` must be listed under `[varnamelist]`.
- `rhlib_script.osc` must be listed under `[script]`.
- `rhlib_init` must be called from the `{init}` entrypoint.
- `rhlib_frame` must be called from both the `{frame}` and `{frame_ai}`
  entrypoints (if the latter exists).

The variables provided by this module should be considered constants and not be
written to.

#### Variables

`rhlib_frameRate`
: Approximated average framerate for the last 5 frames. Useful for disabling
  something computationally-intensive at low framerates.

`rhlib_frameTime`
: A sanitised clone of `Timegap` - cannot be higher than 0.1 seconds, preventing
  runaway values at low framerates.

`rhlib_runTime`
: Cumulative duration of all frames this script has experienced so far. Unlike
  `Time` or `GetTime`, this variable does not jump or reset. Also saves
  incrementing or decrementing numerous variables across different scripts by
  `Timegap` each frame.

#### Macros

`rhlib_roundNearest`
: Rounds the top float stack value to the nearest integer value.

`rhlib_roundPositive`
: Rounds the top float stack value to the nearest integer value, always rounding
  up (towards positive infinity).

`rhlib_roundNegative`
: Rounds the top float stack value to the nearest integer value, always rounding
  down (towards negative infinity).

`rhlib_exponential`
: Approaches a target value approximately exponentially. Functionally identical
  to the `traegheit` macros found in the default SD/D cockpit and door scripts,
  and to the `engine_traegheit` macro found in the default NL/NG engine scripts.
  Register 0 should contain the target, register 1 the current value, register 2
  the positive approach constant and register 3 the negative approach constant.
  After execution, register 1 contains the new value.

### Time

To use this module:

- `rhlib_time_varlist.txt` must be listed under `[varnamelist]`.
- `rhlib_time_script.osc` must be listed under `[script]`.
- `rhlib_time_frame` must be called from both the `{frame}` and `{frame_ai}`
  entrypoints (if the latter exists).

The variables provided by this module should be considered constants and not be
written to.

`rhlib_time`
: The number of whole seconds since midnight (integer version of system variable
  `Time`).

`rhlib_time_updated`
: 1 during any frame in which the value of `rhlib_time` changed, 0 otherwise.

`rhlib_time_hour12`, `rhlib_time_hour24`, `rhlib_time_minute` and
`rhlib_time_second`
: The hours, minutes and seconds components of a 12 or 24-hour digital clock
  display.

`rhlib_time_pm`
: 1 between noon (inclusive) and midnight (exclusive), 0 otherwise.

### Timetable

To use this module:

- `rhlib_tt_varlist.txt` must be listed under `[varnamelist]`.
- `rhlib_tt_script.osc` must be listed under `[script]`.
- `rhlib_tt_frame` must be called from both the `{frame}` and `{frame_ai}`
  entrypoints (if the latter exists).

The variables provided by this module should be considered constants and not be
written to.

`rhlib_tt_index`
: The index of the current bus stop in the timetable. Same as the output of
  `(M.V.GetTTBusstopIndex)`.

`rhlib_tt_active`
: 1 if there is an active timetable, 0 otherwise. This value should be the same
  as, but is not set to, the value of `schedule_active`.

`rhlib_tt_updated`, and `rhlib_tt_advanced`, `rhlib_tt_started` and
`rhlib_tt_stopped`
: `rhlib_tt_updated`, and one of `rhlib_tt_advanced`, `rhlib_tt_started` or
  `rhlib_tt_stopped` are 1 during any frame in which the value of
  `rhlib_tt_index` changed, with the latter indicating the nature of the change,
  0 otherwise.
