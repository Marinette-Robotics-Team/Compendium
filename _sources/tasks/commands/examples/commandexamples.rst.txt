Examples of Commands
====================

 - `Climber <commands/climber.html>`_
 	Manual control of a motor that is programmed to never spin backwards.

 - `Tank Drive <commands/tankdrive.html>`_
 	A basic tank drive. Left joystick controls the left wheels, right joystick controls the right wheels.

 - `Ball Acquisition <commands/bacq.html>`_
 	A command that prevents balls from accidentally falling out of a claw using intake wheels.

 - `Open and Close a Claw <commands/claw.html>`_
 	Opening or closing a claw using pneumatics.

 - `Moving an Elevator Manually <commands/elevatormanual.html>`_
 	Raising and lowering an elevator subsystem, in between two limit switches marking the bottom and the top.

 - `Driving Forward a Certain Distance <commands/drivesetpoint.html>`_
 	Accurately driving the robot forwards to a certain distance using encoders and a PIDController.

 - `Rotating a Claw both Manually and using Setpoints <commands/aimclaw.html>`_
 	Two commands that interact to give an operator both manual and setpoint control of a rotating claw that can only rotate between two limits in a safe way, using a Potentiometer and a PIDController.

.. toctree::
	:glob:
	:maxdepth: 10
	:caption: Examples
	:hidden:
	
	commands/climber
	commands/tankdrive
	commands/bacq
	commands/claw
	commands/elevatormanual
	commands/drivesetpoint
	commands/aimclaw