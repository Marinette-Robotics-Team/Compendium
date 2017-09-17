CommandGroups
=============

Command groups are simply groups of commands, chained together.
To chain together commands, use the addSequential(Command) method in the CommandGroup’s constructor.

Example:

.. code-block:: java

	addSequential(new DriveForward(1000, 10));
	addSequential(new OpenGrabber());
	addSequential(new DriveForward(-1000, 10));

It is also possible to run two commands at once in a command group, using addParallel. Then, all of the commands added using addParallel will start when the next addSequential starts.

Example:

.. code-block:: java

	addSequential(new DriveForward(1000, 10));
	addParallel(new LiftRake());
	addParallel(new IntakeOut());
	addSequential(new OpenGrabber()); // will run LiftRake and IntakeOut
	addSequential(new DriveForward(-1000, 10));

Note that a CommandGroup is also a Command, and thus can require a subsystem, be interrupted, and have initialize, execute, isFinished, end, and interrupt methods.

CommandGroups are often used for autonomous, or sequences of commands that must be performed together.

.. toctree::
	:glob:
	:maxdepth: 10
	:caption: Contents: