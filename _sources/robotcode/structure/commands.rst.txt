Commands
========

A command is simply an object that acts like a loop that happens over time. At its most basic level, a command is essentially:

.. code-block:: java

	initialize();
	while(!isFinished())
	{
	    execute();
	}
	end(); // or interrupted() if necessary

However, the interesting part is that execute runs approximately once every 50ms and also that many commands can run at the same time.

Additionally, since many commands run at the same time, you do not want two commands trying to use the same subsystem at the same time, since that does not work (e.g. setting the same motor two different values at the same time).

To solve this problem, use the requires(Subsystem) method in the constructor of the command.

When another command that requires the same subsystem starts, it will interrupt the one previously running, ending it. That way, no two commands that run on the same subsystem will be running at the same time.

 - In the class:
	Declare fields here. These should usually be private.
 - Constructor:
	Pass in parameters and initialize fields. Note that this runs when constructed ONLY, which only happens once for each instance of a command. For code that runs each time the command starts, use initialize().
 - initialize():
	This method runs when the command is started, either by being a subsystem’s default command, command.start(), or being the target of a button.
	Reset the command here so that it is ready to run again.
 - execute():
	This method runs approximately every 50ms while the command is active. Put your main loop logic here.
 - isFinished():
	Return whether or not the command should end if never interrupted. For some commands, such as DriveContinuous, that may be never. For some commands, such as driving to a certain distance, that may be when the robot is a certain distance away from a setpoint.
 - end():
	This method is called when the command ends naturally via the isFinished() method returning true. It is good practice to reset the command here.
 - interrupted():
	This method is called when the command is interrupted by another command that requires the same subsystem. It is good practice to usually just add an end() call here.

Commands go in the org.usfirst.frc.teamXXXX.robot.commands package.

More Command Types
^^^^^^^^^^^^^^^^^^

.. toctree::
	:glob:
	:maxdepth: 10
	:titlesonly:
	
	morecommands