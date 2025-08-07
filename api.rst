SSM Lua Library – API Reference
===============================

This document describes the public API of the Synchronous Sequential Model (SSM) Lua runtime,
including modules for scheduling, simulation, priority management, and reactive channels.

::
  
  NOTE   This API reference was converted from Lua source annotated with
         LDoc-style comments, with assistance from Microsoft Copilot.

Type Definitions
----------------

.. list-table::
   :header-rows: 1

   * - Type
     - Description
   * - ``BasePriority``
     - A root priority object from which all other priorities are derived.
   * - ``Channel``
     - A reactive table abstraction with delayed updates.
   * - ``CTable``
     - A table with an attached ``Channel`` metatable.
   * - ``Duration``
     - An integer representing a time interval.
   * - ``Event``
     - A tuple ``{Time, value}`` representing a scheduled update.
   * - ``Key``
     - Any valid Lua table key.
   * - ``IsSched``
     - Either ``true`` or a table mapping ``Key`` to ``true``.
   * - ``LogicalTime``
     - A subclass of ``Timestamp`` used for logical scheduling.
   * - ``PhysicalTime``
     - A subclass of ``Timestamp`` used for real-world time tracking.
   * - ``Priority``
     - An object representing a position in a total order.
   * - ``Process``
     - A coroutine-based unit of execution.
   * - ``Timestamp``
     - A number representing a point in logical or physical time.


Module: ``ssm``
---------------

Time Resolution
---------------
  
===========================  ================================================
Function                     Description
===========================  ================================================
days(t: number)              Converts a number to days.
hr(t: number)                Converts a number to hours.
min(t: number)               Converts a number to minutes.
msec(t: number)              Converts a number to milliseconds.
nsec(t: number)              Converts a number to nanoseconds.
sec(t: number)               Converts a number to seconds.
usec(t: number)              Converts a number to microseconds.
===========================  ================================================

=======================  ====================================================
Constant                 Description
=======================  ====================================================
always: LogicalTime      A timestamp representing the beginning of time; smaller than any other timestamp.
never: LogicalTime       A timestamp representing the end of time; larger than any other timestamp.
=======================  ====================================================

Duration Type
-------------

=============================  ====================================================
Method / Property              Description
=============================  ====================================================
Duration                       Represents a span of logical time.
Duration.ticks                 Number of ticks in the duration.
Duration.__add__(other)        Adds two durations.
Duration.__sub__(other)        Subtracts one duration from another.
Duration.__mul__(factor)       Multiplies the duration by a scalar.
Duration.__truediv__(factor)   Divides the duration by a scalar.
Duration.__eq__(other)         Checks equality between durations.
Duration.__lt__(other)         Checks if one duration is less than another.
Duration.__le__(other)         Checks if one duration is less than or equal to another.
=============================  ====================================================

LogicalTime Type
----------------

=============================  ====================================================
Method / Property              Description
=============================  ====================================================
LogicalTime                    Represents a point in logical time.
LogicalTime.ticks              Number of ticks since time zero.
LogicalTime + Duration         Adds a duration to a logical time.
LogicalTime - Duration         Subtracts a duration from a logical time.
LogicalTime - LogicalTime      Returns the duration between two logical times.
LogicalTime.__eq__(other)      Checks equality between logical times.
LogicalTime.__lt__(other)      Checks if one logical time is earlier than another.
LogicalTime.__le__(other)      Checks if one logical time is earlier or equal.
=============================  ====================================================

Routine Class
-------------

=============================  ====================================================
Method                         Description
=============================  ====================================================
Routine(*args, **kwargs)       Represents an SSM routine that can be called, spawned, or deferred.
Routine.__call__(*args)        Calls the routine directly from the current process.
Routine.spawn(*args)           Spawns the routine in a new process with higher priority.
Routine.defer(*args)           Defers the routine to a lower priority process.
=============================  ====================================================

Event Class
-----------

=============================  ====================================================
Method / Property              Description
=============================  ====================================================
Event                          Represents a synchronization primitive for signaling.
Event.is_signaled              Returns whether the event has been signaled.
Event.signal()                 Signals the event, waking up waiting processes.
Event.wait()                   Waits for the event to be signaled.
=============================  ====================================================

Process Class
-------------
  
=============================  ====================================================
Method / Property              Description
=============================  ====================================================
Process                        Represents a running SSM process.
Process.time                   Current logical time of the process.
Process.sleep(duration)        Suspends the process for a duration.
Process.await(event)           Waits for an event to be signaled.
Process.spawn(routine)         Spawns a new process to run a routine.
=============================  ====================================================
