More Command Types
==================

Although Command and CommandGroup are the most commonly used types of commands, WPI Library provides some more command classes that may be useful.

Note that some of these may not work in FRC Simulator, so you may have to implement these yourself.

Commonly used
-------------

`InstantCommand <morecommands/instant.html>`_
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This command type runs once, then finishes. It's useful for commands that are just one-time method calls, which many are.

`WaitCommand <morecommands/wait.html>`_
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Although our team commonly re-invents the wheel by creating our own SleepCommand, we could just use WaitCommand instead. This command waits a given number of seconds. This is mainly useful for autonomous timing, but can also be used to make sure a pneumatic cylinder has finished moving before moving other parts to prevent damage.

`PrintCommand <morecommands/print.html>`_
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

This command type simply prints out a message to the riolog. This is mainly used for debugging autonomous or other command groups, to check when certain commands start and finish by printing out messages before they start or after they finish by putting PrintCommands in the command group.

Never used
----------

ConditionalCommand
^^^^^^^^^^^^^^^^^^^^^^^^^^

This command type runs one command if the condition is true, and the other command if the condition is false. This is useful for commands that are designed to toggle something when pressed. However, the main issue is that using a ConditionalCommand to implement a toggle is clunky. It would be better to either make a subsystem method for this, or just use a normal command with an if statement.

TimedCommand
^^^^^^^^^^^^^^^^^^^^^^^^^^

This command type runs for a certain amount of time, but finishes. This is essentially a WaitCommand, except you can run things in execute() and the like during this command. However, I have never used this, since if a timeout is necessary, it often also needs a more complex, custom implementation in a normal Command using a Timer.

StartCommand
^^^^^^^^^^^^^^^^^^^^^^^^^^

This command type starts the command passed into its constructor. All it does is start another command and finish immediately, so this command seems pretty useless. Why not just start the original command?  I've never had any reason to use this.

WaitForChildren
^^^^^^^^^^^^^^^^^^^^^^^^^^

This command type is only useful in a CommandGroup. Otherwise, it finishes immediately. In a CommandGroup, it waits for all of the commands running from addParallel before finishing. That way, you can make sure all of your parallel commands finish before moving along in your addSequential line. Although it can be useful in certain situations, I've never had to use this. Usually, WaitCommand is better suited.

WaitUntilCommand
^^^^^^^^^^^^^^^^^^^^^^^^^^

This command type waits until an absolute game time, such as 120 seconds on the match clock. There has never really been a reason to keep track of the match clock, so I have never used this command type.

PIDCommand
^^^^^^^^^^^^^^^^^^^^^^^^^^

This command type has a build in PID Controller. However, it's actually easier and neater to just use the PID Controller in the subsystem anyway, so this command type isn't very useful.

.. toctree::
	:glob:
	:maxdepth: 10
	:hidden:

	morecommands/instant
	morecommands/wait
	morecommands/print