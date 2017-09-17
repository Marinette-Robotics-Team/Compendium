Drive Trains
============

This section contains tutorials on how to program some of the more advanced drivetrains. Note that this is a guide for more advanced users.

If you're instead looking for a simple tank drive, see the `Tank Drive subsystem <./../subsystems/examples/tankdrive.html>`_, as well as its `DriveContinuous <./../commands/examples/commands/tankdrive.html>`_ and `DriveForwards <./../commands/examples/commands/drivesetpoint.html>`_ commands.

**Note:** I am aware of WPILibJ's `RobotDrive class <http://first.wpi.edu/FRC/roborio/beta/docs/java/edu/wpi/first/wpilibj/RobotDrive.html>`_. However, using the RobotDrive class is not very customizable. Being able to plug in your own motor power curves to fix issues like friction in the system and joystick deadzones is very useful, so although it is easy to use the RobotDrive class, I wouldn't recommend it.

.. toctree::
	:glob:
	:maxdepth: 10
	:caption: Contents
	:titlesonly:

	advancedtank
	mecanum
	swerve