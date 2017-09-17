PrintCommand
============

This command simply prints out a certain string to the riolog. It is mostly used for debug.

Example:

.. code-block:: java

	addSequential(new PrintCommand("Starting autonomous"));
	addSequential(new DriveForward(1000.0));
	addSequential(new PrintCommand("Finished Drive Forward A, dropping gear"));
	addSequential(new OpenClaw());
	addSequential(new PrintCommand("Claw Open, starting drive backwards"));
	addSequential(new DriveForwards(-1000.0));
	addSequential(new PrintCommand("finished autonomous"));

In this code, if the autonomous gets stuck at a certain command, we can tell which one it is stuck at by looking at the riolog.

There isn't really any other use for PrintCommand other than debugging autonomous or some other CommandGroup.

.. toctree::
	:glob:
	:maxdepth: 10

