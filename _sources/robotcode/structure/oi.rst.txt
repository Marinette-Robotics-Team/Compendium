OI
==

OI stands for Operator interface. This static class contains all the components pertaining to driver input. For example, Joysticks, Joystick buttons and their event handlers go here.

For more information on using Joysticks and driver input, see `Joysticks <./../components/sensors/joysticks.html>`_.

 - In the class:
	Joystick and Button declarations. Everything should be static.

 - Constructor, or a static initializer
	Joystick and Button initialization, and binding of commands to buttons.

Each controller has a port in the Driver Station, which is used in initialization. For example, to use port 0, commonly used for the Driver, use:

.. code-block:: java

	public static Joystick driver = new Joystick(0);

Remember that everything in the class should be static.

Example for a button on a controller:

.. code-block:: java

	Button driverA = new JoystickButton(driver, 1);
	driverA.whenPressed(new DriveBackwards(1000, 10));

Note that button numbers are different for every controller type. The button number will have to be found experimentally on the Driver Station.

Note that button presses other than a simple whenPressed or whenReleased is often very difficult to do with only button event handlers. You may have to get creative with ConditionalCommand or something similar to make things work.

Very rarely, it may be necessary to use limit switches or other non-button items as buttons with event handling. Those event handlers can also be placed here.

.. toctree::
	:glob:
	:maxdepth: 10
	:caption: Contents: