SSM Lua Library – API Reference
===============================

This document describes the public API of the Synchronous Sequential Model (SSM) Lua runtime,
including modules for scheduling, simulation, priority management, and reactive channels.

Time Definitions
----------------

Comments/questions:

*   The time definitions can be found in ``lua-ssm/lua/ssm/core.lua``.
*   Why is Duration an ``integer`` and Timestamp a ``number``?

=============================  ====================================================
Type                           Description
=============================  ====================================================
Duration                       An ``integer``, represents a span of time (logical or physical), measured in ticks.
LogicalTime                    Represents a point in logical time, comparable and arithmetic.
PhysicalTime                   Represents a point in "wall-clock" time
Timestamp                      A ``number``, represents a point in time (either logical or physical) 
=============================  ====================================================

A comment on time...

  "The core library is resolution-agnostic, but for cross-platform accuracy we
  will standardize to nanoseconds as our base resolution. Each backend will
  need to adjust from nanoseconds accordingly." - from ``ssm.lua``

Types
-----

CTable
  Abbreviated notation used instead of ChannelTable.

any
  Any type.

boolean, function, integer, nil, number, table
  The respective standard Lua types.

API
---

``Channel(init: table, no_clobber: boolean): CTable``
  Creat a channel table from an initial value.

``after(self: CTable, d: Duration, kvs: table): number``
  **A Channel method**

  It schedules a delayed assignment of the values to the keys defined in kvs. The delay, d, is a Duration value. Calling ``after()`` is non-blocking.

``as_msec(d: Duration|Timestamp): number``
  Converts Duration to number of msec

``as_nsec(d: Duration|Timestamp): number``
  Converts Duration to number of nsec

``as_sec(d: Duration|Timestamp): number``
  Converts Duration to number of sec

``as_usec(d: Duration|Timestamp): number``
  Converts Duration to number of usec

``defer(f: function, ...: any): CTable``
  Create a process from a function and passes it the remaining arguments (...), at a lower priority than the caller.
  The newly spawned process will executes its inaugural instant after the caller waits or terminates.

  If f has been added to ``ssm`` then can use method format: ``f:defer(...)``

``get(self: CTable): any``
  **A Channel method**

  Gets the channel self[1] value.

``ipairs(t: table): <ipairs iterator>``
  An ipairs iterator that works for table with overloaded __ipairs

``join(tbls: {Table}): any|{any}``
  Wait for all return channels to be updated, and unpack the results.

  If multiple values are returned on an individual return channel, those
  return values are packed into an array.

``last_updated(self: CTable, key: any|nil): LogicalTime``
  **A Channel method**

  Obtain the last time a channel table (or one of its fields) was modified.

  If key is nil, this method will return the earliest timestamp among all
  the table's fields.

  If there is no value indexed by key, this method returns nil.

``msec(t: number): Duration``
  Converts numer of milliseconds to Duration.

``never: inf``
   A constant value. The Timestamp representing the end of time; larger than any other timestamp.

``now(): Timestamp``
  Obtains the current logical time.

``nsec(t: number): Duration``
  Converts numer of nanoseconds to Duration.

``pairs(t: table): <pairs iterator>``
  A pairs iterator that works for table with overloaded __pairs

``sec(t: number) : Duration``
  Converts numer of seconds to Duration.

``set(self: CTable, val: any)``
  **A Channel method**

  Sets the channel self[1] value to val.

``set_active()``
  Mark the current running process as active.

``set_passive()``
  Mark the current running process as passive.

``spawn(f: function, ...: any): CTable``
  Create a process from a function and passes it the remaining arguments (...), at a higher priority than the caller.
  The caller will suspend as the newly spawned process executes its inaugural
  instant; the caller will resume execution in the same instant after the new
  process waits or terminates.

  If f has been added to ``ssm`` then can use method format: ``f:spawn(...)``

``start(entry: function(...: any): R, ...: any ): time: LogicalTime, val: R``
  Executes the entry in a synchronous context, starting at time == 0. Can pass agruments ``...``.
  Returns the completion time and the return value of type R.

``unpack(t: table, i: number|nil): ...``
  A table unpack that works for table with overloaded __index

``usec(t: number): Duration``
  Converts numer of microseconds to Duration.

``wait(wait_spec: CTable|{CTable}): {boolean}``
  Wait for one or more channel tables to be updated. Returns a list of booleans, where ``true`` indicates which wait specificications were met.
  
  Wait for updates on some number of channel tables.
  Each argument is a "wait specification", which is either be a channel table
  or an array of channel tables. A wait specification is satisfied when all
  channel tables therein have been assigned to (not necessarily in the same
  instant).

  wait() unblocks when at least one wait specification is satisfied. It will
  return multiple boolean return values, positionally indicating whether each
  wait specification in the argument was satisfied.

  In other words, wait(a, {b, c}) will unblock when a is updated, or both
  b and c are updated.

Type Definitions
----------------

=============================  ====================================================
Type                           Description
=============================  ====================================================
Routine                        A callable object representing a schedulable SSM routine.
Event                          A synchronization primitive used for signaling between processes.
Process                        Represents a running SSM process with access to time and control flow.
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

