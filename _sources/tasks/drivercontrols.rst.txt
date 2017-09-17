Switching around Driver Controls in OI
======================================

If a driver requests that you change around the drive controls, this often is just switching around the buttons are linked to various commands.

Example:

Imagine that your robot has a claw that grabs garbage cans. But, the claw can also rotate the garbage can it's holding.

Imagine that currently, X on the operator controller rotates the container 90 degrees (a quarter of the way) around, and A on the operator controller rotates the container 180 degrees (halfway) around.

Then, the operator says: "I want the X and A buttons switched, since I rotate the bin a quarter of the way around more frequently, so I want it to be on the A button."

Here is an example OI:

.. code-block:: java
	:emphasize-lines: 22,23

	/**
	 * This class is the glue that binds the controls on the physical operator
	 * interface to the commands and command groups that allow control of the robot.
	 */
	public class OI
	{
	    public static Joystick operator;
	    public static Joystick driver;

	    public OI()
	    {
	        driver = new Joystick(1);
	        operator = new Joystick(2);

	        // Driver Buttons
	        Button slowButton = new JoystickButton(driver, 5);
	        Button fastButton = new JoystickButton(driver, 6);
	        Button runDriveForwards = new JoystickButton(driver, 2);
	        Button theMorePlan = new JoystickButton(driver, 4);

	        // Operator Buttons
	        Button quarterRotate = new JoystickButton(operator, 4);
	        Button fullRotate = new JoystickButton(operator, 1);
	        Button grabButton = new JoystickButton(operator, 3);
	        Button resetRotate = new JoystickButton(operator, 2);
	        Button elevatorManualControl = new JoystickButton(operator, 5);
	        Button toggleDrawbridge = new JoystickButton(operator, 6);
	        Button moveRake = new JoystickButton(operator, 7);
	        Button dropRake = new JoystickButton(operator, 10);
	        Button manualRaking = new JoystickButton(operator, 8);
	        Button grabberInterrupt = new JoystickButton(operator, 12);

	        // Bind Commands to Buttons
	        runDriveForwards.whileHeld(new MoveBackwards());
	        slowButton.whenPressed(new DriveContinuous(0.4));
	        fastButton.whenPressed(new TransitionGear(0.4, 1.0, 1000));
	        theMorePlan.whenPressed(new DriveForward(600.0, 10.0));

	        elevatorManualControl.whileHeld(new ElevatorControlContinuous());
	        toggleDrawbridge.whenPressed(new BridgeUpFixRake());
	        quarterRotate.whenPressed(new RotateContainer(90));
	        fullRotate.whenPressed(new RotateContainer(180));
	        resetRotate.whenPressed(new RotateContainer(-90.0));
	        grabButton.whenPressed(new ObjectGrabber());
	        moveRake.whenPressed(new RakeTurnCommandGroup());
	        dropRake.whenPressed(new BridgeDownFixRake());
	        grabberInterrupt.whenPressed(new ManualInterrupt(Robot.grabber));

	        manualRaking.whenPressed(new RakeTurnCommandGroup());
	    }
	}

Determine which buttons to switch. In this example, they are the quarterRotate and fullRotate buttons, which are already highlighted.

.. code-block:: java

    Button quarterRotate = new JoystickButton(operator, 4); // X button
    Button fullRotate = new JoystickButton(operator, 1);    // A button

Now, all you need to do is to switch the button numbers, so that the A button, or button 1, goes to quarterRotate, and that the X button, or button 4, goes to fullRotate.

.. code-block:: java

    Button quarterRotate = new JoystickButton(operator, 1); // A button
    Button fullRotate = new JoystickButton(operator, 4);    // X button

And that's it! Now, the buttons have been switched.

Keep in mind that more complex driver controls changes may require the addition of new commands, and may not be able to be fixed with a simple button switch.

This skill of switching buttons is also useful when a different brand of controller has to be used, since each brand and model of controller may have its own unique button layout.

.. toctree::
	:glob:
	:maxdepth: 10
	:caption: Contents