Subsystems
==========

At its most basic level, a subsystem is simply something that controls how commands run. Commands, to avoid conflicts where many commands try to access the same resource, use requires(Subsystem) to interrupt the previous command.

Subsystems can also define a default command. Then, as long as the subsystem is constructed, if no other command that requires that subsystem is running, the default command will run. For example, the Drive subsystem’s default command is often DriveContinuous, which gives the driver control over the robot.

However, a subsystem also holds the components that its commands use. For example, the Drive subsystem would hold the speed controllers that turn the robot’s drive wheels, the encoders that measure drive distance, and any PID controllers used to control the robot’s driving.

Subsystems, similar to RobotMap and OI, should be static, meaning that all of their components should be static.

 - In the class:
	Declare all of the components of that subsystem, including SpeedControllers, Solenoids, Sensors, PID Controllers, and any utilities used.
 - Constructor:
	Initialize all of the components of that subsystem.
 - initDefaultCommand():
	Set the subsystem’s default command. Leave this blank if there shouldn’t be a default command for that subsystem.
	Example:
	
	.. code-block:: java
	
		setDefaultCommand(new DriveContinuous());

Note that it is good practice to make the subsystem’s components private, and then only expose public methods that would allow someone to use the subsystem in the correct way.
For example, in the Drive subsystem, if the motors are controlled manually, someone could, for example, accidentally turn the left motors against each other and fry the motors. Instead, it would be smart to only expose a method that turns all of the motors on one side the same direction.


.. toctree::
	:glob:
	:maxdepth: 10
	:caption: Contents: