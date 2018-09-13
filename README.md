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

### Delay

To use this module:

- Time and Timetable modules must be active.
- `rhlib_delay_varlist.txt` must be listed under `[varnamelist]`.
- `rhlib_delay_script.osc` must be listed under `[script]`.
- `rhlib_delay_frame` must be called from both the `{frame}` and `{frame_ai}`
  entrypoints (if the latter exists).

The variables provided by this module should be considered constants and not be
written to.

`rhlib_delay`
: The delay against the timetable in whole seconds (`(M.V.GetTTDelay)` rounded
  towards zero).

`rhlib_delay_updated`
: 1 during any frame in which the value of `rhlib_delay` changed, 0 otherwise.

`rhlib_delay_direction`, `rhlib_delay_minute` and `rhlib_delay_second`
: The +/- indicator, minutes and seconds components of a digital clock display
  for the timetable delay.

### AI State

To use this module:

- `rhlib_aiState_varlist.txt` must be listed under `[varnamelist]`.
- `rhlib_aiState_script.osc` must be listed under `[script]`.
- `rhlib_aiState_init` must be called from the `{init}` entrypoint.
- `rhlib_aiState_frame` must be called from the `{frame}` entrypoint.
- `rhlib_aiState_frame_ai` must be called from the `{frame_ai}` entrypoint (if
  one exists).

`rhlib_aiState`
: 0 when the player is driving their bus, 1 when the AI is driving the player's
  bus and 2 when the AI is driving an AI bus. If there is no `{frame_ai}`
  entrypoint, the value will never be 2.

`rhlib_aiState_updated`
: 1 during any frame in which the value of `rhlib_aiState` changed, 0 otherwise.

### Draw

To use this module:

- `rhlib_draw_varlist.txt` must be listed under `[varnamelist]`.
- `rhlib_draw_stringvarlist.txt` must be listed under `[stringvarnamelist]`.
- `rhlib_draw_script.osc` must be listed under `[script]`.
- `rhlib_draw_init` must be called from the `{init}` entrypoint.

Using the system macros directly, values must be loaded into the stack ahead of
every call, resulting in a lot of repetition. This module's macros allow you to
set a variable only when its value needs to change, improving readability.

`rhlib_draw_lock`
: Locks the script texture, ready for drawing.

`rhlib_draw_unlock`
: Unlocks the script texture, ready for display.

`rhlib_draw_clear`
: Unlocks, Resets, and Locks the script texture.

`rhlib_draw_safeTextureLoad`
: Unlocks, Loads a texture into, and Locks the script texture.

`rhlib_draw_generateMipmaps`
: Generates mipmaps for the script texture.

`rhlib_draw_black`
: Sets the color variables to 0.

`rhlib_draw_white`
: Sets the color variables to 255.

`rhlib_draw_setColor`
: Sets the drawing color from the color variables.

`rhlib_draw_getColor`
: Reads the drawing color into the color variables. Additionally returns 1 if
  the variables were not changed, 0 otherwise.

`rhlib_draw_readPixel`
: Sets the drawing color to that of a pixel.

`rhlib_draw_pixel`
: Draws a single pixel.

`rhlib_draw_line`
: If `w` is non-zero, draws a rectangle of width `w` and height `t`.
  If `h` is non-zero, draws a rectangle of width `t` and height `h`.

`rhlib_draw_rect`
: Draws a rectangle of width `w` and height `h`.

`rhlib_draw_border`
: Draws a border of thickness `t` around a rectangle of width `w` and height
  `h`. Four rectangles are drawn to achieve this without overwriting the central
  rectangle.

`rhlib_draw_rect_border`
: Draws a rectangle the same size as the outside of `rhlib_draw_border`. Can be
  followed by `rhlib_draw_rect` with the same parameters but a different drawing
  color to create a rectangle with border.

`rhlib_draw_textLength`
: Sets `textLength` to and returns the length of the text in the current font.

`rhlib_draw_leftText`
: Draws left-aligned text.

`rhlib_draw_rightText`
: Draws right-aligned text. Requires `w` and `textLength` to be set.

`rhlib_draw_centerText`
: Draws center-aligned text. Requires `w` and `textLength` to be set.
