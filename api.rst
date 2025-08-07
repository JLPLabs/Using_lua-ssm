SSM Lua Library – API Reference
===============================

This document describes the public API of the Synchronous Sequential Model (SSM) Lua runtime,
including modules for scheduling, simulation, priority management, and reactive channels.

::
  
  NOTE   This API reference was converted from Lua source annotated with
         LDoc and EmmyLua comments, with assistance from Microsoft Copilot.



Time Definitions
----------------

These definitions can be found in ``lua-ssm/lua/ssm/core.lua``, with a few details extrapolated by the author.

::

  Comments/questions:
  [?] why is Duration an ``integer`` and Timestamp a ``number`` ?

=============================  ====================================================
Type                           Description
=============================  ====================================================
Duration                       An ``integer``, represents a span of time (logical or physical), measured in ticks.
LogicalTime                    Represents a point in logical time, comparable and arithmetic.
PhysicalTime                   Represents a point in "wall-clock" time
Timestamp                      A ``number``, represents a point in time (either logical or physical) 
=============================  ====================================================

``ipairs(t: table) -> ipairs iterator``
  An ipairs iterator that works for table with overloaded __ipairs

Utlity Functions
----------------

The "lua" library in ``lua-ssm/lua/ssm/lib/lua.lua`` exists solely to provide these helpers.

========================================  ================================================
Function Signature                        Description
========================================  ================================================
ipairs(t: table) -> ipairs iterator       an ipairs iterator that works for table with overloaded __ipairs
pairs(t: table) -> pairs iterator         a pairs iterator that works for table with overloaded __pairs
unpack(t: table, i: number|nil)  -> ...   a table unpack that works for table with overloaded __index
========================================  ================================================


Time Resolution
---------------

  "The core library is resolution-agnostic, but for cross-platform accuracy we
  will standardize to nanoseconds as our base resolution. Each backend will
  need to adjust from nanoseconds accordingly." - from ``ssm.lua``

========================================  ================================================
Function Signature                        Description
========================================  ================================================
nsec(t: number) -> Duration               Converts numer of nanoseconds to Duration.
usec(t: number) -> Duration               Converts numer of microseconds to Duration.
msec(t: number) -> Duration               Converts numer of milliseconds to Duration.
sec(t: number)  -> Duration               Converts numer of seconds to Duration.
as_nsec(d: Duration|Timestamp) -> number  Converts Duration to number of nsec
as_usec(d: Duration|Timestamp) -> number  Converts Duration to number of usec
as_msec(d: Duration|Timestamp) -> number  Converts Duration to number of msec
as_sec(d: Duration|Timestamp)  -> number  Converts Duration to number of sec
========================================  ================================================

Type Definitions
----------------

=============================  ====================================================
Type                           Description
=============================  ====================================================
Routine                        A callable object representing a schedulable SSM routine.
Event                          A synchronization primitive used for signaling between processes.
Process                        Represents a running SSM process with access to time and control flow.
=============================  ====================================================


Logical Time Constants
----------------------

=============================  ====================================================
Constant                       Description
=============================  ====================================================
never: LogicalTime             A timestamp representing the end of time.
always: LogicalTime            A timestamp representing the beginning of time.
=============================  ====================================================


Duration Type
-------------

===============================  ====================================================
Properties and Methods           Description
===============================  ====================================================
Duration.ticks: integer          Number of ticks in the duration.
__add__(Duration) -> Duration    Adds two durations.
__sub__(Duration) -> Duration    Subtracts one duration from another.
__mul__(number) -> Duration      Multiplies the duration by a scalar.
__truediv__(number) -> Duration  Divides the duration by a scalar.
__eq__(Duration) -> boolean      Checks equality between durations.
__lt__(Duration) -> boolean      Checks if one duration is less than another.
__le__(Duration) -> boolean      Checks if one duration is less than or equal to another.
===============================  ====================================================

LogicalTime Type
----------------

===============================  ====================================================
Properties and Methods           Description
===============================  ====================================================
LogicalTime.ticks: integer       Number of ticks since time zero.
'+ Duration' -> LogicalTime      Adds a duration to a logical time.
'- Duration' -> LogicalTime      Subtracts a duration from a logical time.
'- LogicalTime' -> Duration      Returns the duration between two logical times.
__eq__(LogicalTime) -> boolean   Checks equality between logical times.
__lt__(LogicalTime) -> boolean   Checks if one logical time is earlier than another.
__le__(LogicalTime) -> boolean   Checks if one logical time is earlier or equal.
===============================  ====================================================

Routine Class
-------------

===============================  ====================================================
Method Signature                 Description
===============================  ====================================================
Routine(...) -> Routine          Represents an SSM routine.
__call__(...) -> any             Calls the routine directly.
spawn(...) -> Process            Spawns the routine in a new process.
defer(...) -> Process            Defers the routine to a lower priority process.
===============================  ====================================================

Event Class
-----------

===============================  ====================================================
Method Signature                 Description
===============================  ====================================================
signal()                         Signals the event.
wait()                           Waits for the event to be signaled.
is_signaled -> boolean           Returns whether the event has been signaled.
===============================  ====================================================

Process Class
-------------

===============================  ====================================================
Properties and Methods           Description
===============================  ====================================================
time: LogicalTime                Current logical time of the process.
sleep(d: Duration)               Suspends the process for a duration.
await(e: Event)                  Waits for an event to be signaled.
spawn(r: Routine) -> Process     Spawns a new process to run a routine.
===============================  ====================================================
