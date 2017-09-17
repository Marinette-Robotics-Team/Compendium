RobotMap
========

This static class contains all the pin mappings for each hardware component.
Later, subsystems will reference RobotMap to get their pin mappings when initializing each component of the robot.

For example, RobotMap would contain:

.. code-block:: java

	public static final int DRIVE_LEFT_FRONT_PIN = 3;
	
And then in the Drive subsystem, there would be something like:

.. code-block:: java

	LEFT_FRONT = new Talon(RobotMap.DRIVE_LEFT_FRONT_PIN);

DO NOT put the actual components, such as Talons and Solenoids, into RobotMap. RobotMap is for pins only. Leave the actual components to their respective subsystems.

Also remember that everything in this class should be static and final.

For DoubleSolenoids, remember that there are 2 pins per DoubleSolenoid, so there will need to be a pin A and a pin B for each.

Make sure to sort the pins either by pin type (PWM, DIO, CAN) or by subsystem.
Advantages of sorting by pin type is that it is easy to compare with Electrical’s wiring map, and it is easy to find conflicts.
Advantages of sorting by subsystem is that it is easy to find where each pin is without the use of Ctrl-F, since, for example, the Drive Motors will be close to the Drive Encoders.

Although RobotMap usually doesn’t have a constructor since it only primitives and enums, it should be called in Robot in robotInit() as the first construction anyway for good practice.

.. toctree::
	:glob:
	:maxdepth: 10
	:caption: Contents: