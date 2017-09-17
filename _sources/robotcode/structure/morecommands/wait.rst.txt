WaitCommand
===========

WaitCommand simply is a command that waits for a given number of seconds, then ends.

WaitCommand is also known as SleepCommand, due to our team's many years of accidentally creating WaitCommand ourselves without knowing that it exists, and calling it SleepCommand.

Example:

.. code-block:: java

	addSequential(new DriveForwards(1000.0));  // drive forwards 1000 ticks
	addSequential(new WaitCommand(5.0));       // wait 5 seconds
	addSequential(new DriveForwards(1000.0));  // drive forwards 1000 ticks again

WaitCommand is mainly used in autonomous, to time certain actions.

It can also be used in subsystem code to make sure a pneumatic cylinder has finished extending.

.. code-block:: java

    public class CloseClawSafe extends CommandGroup
    {
        public CloseClawSafe()
        {
            addSequential(CloseClaw());       // close the claw
            addSequential(WaitCommand(0.25)); // wait a quarter of a second to make sure it's closed 
        }
    }

.. toctree::
	:glob:
	:maxdepth: 10

