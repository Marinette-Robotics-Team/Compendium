Open and Close a Claw
=====================

This examples shows how we might program a claw that can open and close based on pneumatics.

Design
------

In this example, the design of the claw is composed of two double action solenoids, one on the left and one on the right, hooked up to pneumatic pistons. When the solenoids are set to retract (reverse), the claw closes, and then they are set to extend (forward), the claw opens.

The goal is to have the claw open when the operator presses the A button, and close when the operator presses the B button. This needs two commands, OpenClaw and CloseClaw.

Subsystem methods
-----------------

This guide assumes that there already is a Claw subsystem, and to set the claw solenoids, you simply need to write:

.. code-block:: java

	Claw.LEFT_SOLENOID.LEFT_SOLENOID.set(DoubleSolenoid.Value.Forward);  // left
	Claw.LEFT_SOLENOID.RIGHT_SOLENOID.set(DoubleSolenoid.Value.Forward); // right

On how to create this subsystem, see `Creating a subsystem <./../../../subsystems/subsystems.html>`_.

We also assume that OI already has a Joystick named operator, and its A and B buttons are buttons 1 and 2.

On how to use OI, see `OI <./../../../../robotcode/structure/oi.html>`_.

Writing the command
-------------------

First, let's start with the OpenClaw command.

Once you create the OpenClaw command, you'll get a blank command template like this:

.. code-block:: java

	package org.usfirst.frc.team93.robot.commands;

	import edu.wpi.first.wpilibj.command.Command;

	/**
	 * 
	 */
	public class OpenClaw extends Command {

	    public OpenClaw() {
	        // Use requires() here to declare subsystem dependencies
	        // eg. requires(chassis);
	    }

	    // Called just before this Command runs the first time
	    protected void initialize() {
	    }

	    // Called repeatedly when this Command is scheduled to run
	    protected void execute() {
	    }

	    // Make this return true when this Command no longer needs to run execute()
	    protected boolean isFinished() {
	        return false;
	    }

	    // Called once after isFinished returns true
	    protected void end() {
	    }

	    // Called when another command which requires one or more of the same
	    // subsystems is scheduled to run
	    protected void interrupted() {
	    }
	}

In the javadoc at the top, describe what this command does.

.. code-block:: java

	/**
	 * This command opens the claw.
	 */

Then, in the constructor, since this command uses the claw, add that the OpenClaw command requires the claw subsystem.

.. code-block:: java

    public OpenClaw()
    {
        // Use requires() here to declare subsystem dependencies
        requires(Robot.claw);
    }

If Robot can't be resolved, make sure to hover over the error and import Robot.

Since we want to open the claw right when this command starts, we open the claw in the initialize() method.

.. code-block:: java

    // Called just before this Command runs the first time
    protected void initialize()
    {
        // open claw, left side
        Claw.LEFT_SOLENOID.LEFT_SOLENOID.set(DoubleSolenoid.Value.Forward);

        // open claw, right side
        Claw.LEFT_SOLENOID.RIGHT_SOLENOID.set(DoubleSolenoid.Value.Forward);
    }

If Claw isn't resolved, make sure to import it.

Since this command already opens the claw right away, there isn't any need for it to do anything in execute, so leave it blank.

Because we never need to run execute(), make sure that isFinished always returns true to finish the command immediately.

.. code-block:: java

    // Make this return true when this Command no longer needs to run execute()
    protected boolean isFinished()
    {
        return true;
    }

This effect can also be achieved with an `InstantCommand <./../../../../robotcode/structure/morecommands/instant.html>`_.

There is also no need to do anything in end() or interrupted(), since the claw was already opened in initialize(), so leave those blank also.

The CloseClaw command
---------------------

Now, make a copy of the OpenClaw command. The CloseClaw command will be the exact same thing, except for in the initialize() method, instead of setting the solenoids to forward, set them to reverse:

.. code-block:: java

    // Called just before this Command runs the first time
    protected void initialize()
    {
        // close claw, left side
        Claw.LEFT_SOLENOID.LEFT_SOLENOID.set(DoubleSolenoid.Value.Reverse);

        // close claw, right side
        Claw.LEFT_SOLENOID.RIGHT_SOLENOID.set(DoubleSolenoid.Value.Reverse);
    }

Running the command
-------------------

Now, go into the OI class. We need to set the claw to open when A is pressed, and the claw to close when B is pressed.

.. code-block:: java
	:emphasize-lines: 9,10,12,13

	public static Joystick driver;
	public static Joystick operator;

	public OI()
	{
	    driver = new Joystick(0);
	    operator = new Joystick(1);

	    Button openClawButton = new JoystickButton(operator, 1);  // A button
	    openClawButton.whenPressed(new OpenClaw());

	    Button closeClawButton = new JoystickButton(operator, 2); // B button
	    closeClawButton.whenPressed(new CloseClaw());
	}

And that's it! Now, whenever the operator presses A, the claw opens, and whenever the operator presses B, the claw closes. 

Final code
----------

OpenClaw
^^^^^^^^

.. code-block:: java

    /**
     * This command opens the claw.
     */
    public class OpenClaw extends Command
    {
        public OpenClaw()
        {
            // Use requires() here to declare subsystem dependencies
            requires(Robot.claw);
        }
    
        // Called just before this Command runs the first time
        protected void initialize()
        {
            // open claw, left side
            Claw.LEFT_SOLENOID.LEFT_SOLENOID.set(DoubleSolenoid.Value.Forward);

            // open claw, right side
            Claw.LEFT_SOLENOID.RIGHT_SOLENOID.set(DoubleSolenoid.Value.Forward);
        }
    
        // Make this return true when this Command no longer needs to run execute()
        protected boolean isFinished()
        {
            return true;
        }
    }

CloseClaw
^^^^^^^^^

.. code-block:: java

    /**
     * This command closes the claw.
     */
    public class CloseClaw extends Command
    {
        public CloseClaw()
        {
            // Use requires() here to declare subsystem dependencies
            requires(Robot.claw);
        }
    
        // Called just before this Command runs the first time
        protected void initialize()
        {
            // close claw, left side
            Claw.LEFT_SOLENOID.LEFT_SOLENOID.set(DoubleSolenoid.Value.Reverse);

            // close claw, right side
            Claw.LEFT_SOLENOID.RIGHT_SOLENOID.set(DoubleSolenoid.Value.Reverse);
        }
    
        // Make this return true when this Command no longer needs to run execute()
        protected boolean isFinished()
        {
            return true;
        }
    }

.. toctree::
	:glob:
	:maxdepth: 10
	:caption: Contents
	
	