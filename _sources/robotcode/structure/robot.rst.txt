Robot
=====

This file contains the basics for running the robot. Everything must somehow be linked to Robot, or else it will not be run. It is comparable to the main method of a normal java program.

 - In the class:
	Declare RobotMap, OI, and all the subsystems (e.g. Drive) and non-subsystem commands (e.g. AutonomousCommand).
 - robotInit():
	Initialize all subsystems and non-subsystem commands.
	It is usually necessary (to avoid NullPointerExceptions) to put RobotMap’s initialization first, and then OI’s initialization last, and the subsystems in between.
	If applicable, also add camera and autonomous chooser initialization.
	For commands that run even when the robot is disabled, make sure to set that here via command.setRunWhenDisabled(true).
 - disabledInit():
	Usually left untouched. Only use if there’s a really good reason.
 - disabledPeriodic():
	Usually left untouched. Only use if there’s a really good reason.
 - autonomousInit():
	Reset sensors and start the autonomous command.
 - autonomousPeriodic():
	Usually the autonomous command does the work here, so this is usually left untouched.
 - teleopInit():
	Cancel the autonomous command.
	Do any teleop mode initialization necessary (e.g. switching drive modes, resetting sensors)
 - teleopPeriodic():
	Usually the subsystems and commands do the work here, so this is usually left untouched.
 - testPeriodic():
	I don’t think I’ve ever used test mode. This is usually left untouched.

**Note:** In both autonomousPeriodic() and teleopPeriodic(), there is this line:

.. code-block:: java

    Scheduler.getInstance().run();

Do not remove or comment out that line, or else commands will not work.

.. toctree::
	:glob:
	:maxdepth: 10
	:caption: Contents: