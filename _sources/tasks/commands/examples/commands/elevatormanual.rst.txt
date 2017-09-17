Moving an Elevator Manually
===========================

This example describes a command that allows the operator to move an elevator subsystem on a robot up and down, in between two limit switches.

Design
------

The elevator moves up and down using a belt that is pulled by a motor hooked up to a Talon SRX speed controller. There are two limit switches at the top and bottom of the elevator. When the elevator moves to the top, the top limit switch is pressed, and the code should not allow any further movement upwards to prevent damage. The same goes for the bottom limit switch. If the bottom limit switch is pressed, the elevator should not be able to move any further downwards. Otherwise, the elevator should be controlled by the operator's left thumbstick.

Subsystem methods
-----------------

This guide assumes that there already is a Elevator subsystem, and to set the elevator motor, you simply need to write:

.. code-block:: java

	Elevator.BELT_MOTOR.set(1.0);

Additionally, to access the limit switches:

.. code-block:: java

	Elevator.TOP_LIMIT_SWITCH.get() // top

and

.. code-block:: java

	Elevator.BOTTOM_LIMIT_SWITCH.get() // bottom

On how to create this subsystem, see `Creating a subsystem <./../../../subsystems/subsystems.html>`_.

We also assume that OI already has a Joystick named operator, whose left thumbstick thumbstick's y axis is axis 1.

On how to use OI, see `OI <./../../../../robotcode/structure/oi.html>`_.

Writing the command
-------------------

Once you create the ElevatorManualControl command, you'll get a blank command template like this:

.. code-block:: java

	package org.usfirst.frc.team93.robot.commands;

	import edu.wpi.first.wpilibj.command.Command;

	/**
	 * 
	 */
	public class ElevatorManualControl extends Command {

	    public ElevatorManualControl() {
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
     * This command gives the operator manual control over the elevator motor.
     * However, if the elevator hits the top limit switch, it cannot go any further up.
     * The same goes for the bottom limit switch.
     * This is a safety measure so that the elevator cannot damage itself.
     */

Then, in the constructor, since this command uses the elevator, make sure this command requires the Elevator subsystem.

.. code-block:: java

    public ElevatorManualControl()
    {
        // Use requires() here to declare subsystem dependencies
        requires(Robot.elevator);
    }

If Robot can't be resolved, make sure to hover over the error and import Robot.

There isn't any need to initialize anything, since all input to comes from the operator joystick. Then, in the execute() method, add the logic of the command.

Now, since we want to set the power of the motors to the thumbstick input, we use OI.operator.getRawAxis(1) to get the left thumbstick value, and then set the elevator motor to that value.

However, we must also make sure that if the elevator is moving up, and the top limit switch is pressed, we instead set the motor to 0. Similarly, if the elevator is moving down, and the bottom limit switch is pressed, we instead also set the motor to 0.

.. code-block:: java

    // Called repeatedly when this Command is scheduled to run
    protected void execute()
    {
        // get joystick value
        double value = OI.operator.getRawAxis(1);

        // set belt motor to joystick value initially
        Elevator.BELT_MOTOR.set(value);

        // but, if the elevator is moving up, and the top limit switch is pressed
        if (value > 0 && Elevator.TOP_LIMIT_SWITCH.get())
        {
            // set the motor to 0
            Elevator.BELT_MOTOR.set(0);
        }

        // but, if the elevator is moving down, and the bottom limit switch is pressed
        if (value < 0 && Elevator.BOTTOM_LIMIT_SWITCH.get())
        {
            // set the motor to 0
            Elevator.BELT_MOTOR.set(0);
        }
    }

If Elevator isn't resolved, make sure to import it.

We always want the operator to have control over the elevator, so this command should never finish. Thus, leave isFinished() as it is.

And, for safety purposes, when this command has to end(), it should stop the belt motor. interrupted() should do the same.

.. code-block:: java

    // Called once after isFinished returns true
    protected void end()
    {
        Elevator.BELT_MOTOR.set(0);
    }

    // Called when another command which requires one or more of the same
    // subsystems is scheduled to run
    protected void interrupted()
    {
        end();
    }

Running the command
-------------------

Now, go into the Elevator subsystem and make ElevatorManualControl the default command of the Elevator subsystem.

.. code-block:: java

	@Override
	public void initDefaultCommand()
	{
	    // Give the operator control of the elevator unless interrupted
	    setDefaultCommand(new ElevatorManualControl());
	}

Make sure that ElevatorManualControl requires Robot.elevator, and that the Elevator subsystem is constructed in the Robot class, or else there will be a IllegalUseOfCommandException.

And that's it! Now, the operator has manual control over the elevator in a safe way.

Final code
----------

.. code-block:: java

    package org.usfirst.frc.team93.robot.commands;

    import org.usfirst.frc.team93.robot.Robot;
    import org.usfirst.frc.team93.robot.subsystems.Elevator;

    import edu.wpi.first.wpilibj.command.Command;

    /**
     * This command gives the operator manual control over the elevator motor.
     * However, if the elevator hits the top limit switch, it cannot go any further up.
     * The same goes for the bottom limit switch.
     * This is a safety measure so that the elevator cannot damage itself.
     */
    public class ElevatorManualControl extends Command
    {
        
        public ElevatorManualControl()
        {
            // Use requires() here to declare subsystem dependencies
            requires(Robot.elevator);
        }
        
        // Called just before this Command runs the first time
        @Override
        protected void initialize()
        {
        }
        
        // Called repeatedly when this Command is scheduled to run
        protected void execute()
        {
            // get joystick value
            double value = OI.operator.getRawAxis(1);
            
            // set belt motor to joystick value initially
            Elevator.BELT_MOTOR.set(value);
            
            // but, if the elevator is moving up, and the top limit switch is pressed
            if (value > 0 && Elevator.TOP_LIMIT_SWITCH.get())
            {
                // set the motor to 0
                Elevator.BELT_MOTOR.set(0);
            }
            
            // but, if the elevator is moving down, and the bottom limit switch is pressed
            if (value < 0 && Elevator.BOTTOM_LIMIT_SWITCH.get())
            {
                // set the motor to 0
                Elevator.BELT_MOTOR.set(0);
            }
        }
        
        // Make this return true when this Command no longer needs to run execute()
        @Override
        protected boolean isFinished()
        {
            return false;
        }
        
        // Called once after isFinished returns true
        protected void end()
        {
            Elevator.BELT_MOTOR.set(0);
        }
    
        // Called when another command which requires one or more of the same
        // subsystems is scheduled to run
        protected void interrupted()
        {
            end();
        }
    }

.. toctree::
	:glob:
	:maxdepth: 10
	:caption: Contents
	
	