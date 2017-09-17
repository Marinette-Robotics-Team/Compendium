Climber
=======

This examples shows how we might program a climber on a robot, by making a command called ClimberContinuous.

Design
------

In this example, the design of the climber is simply a CIM motor that turns a shaft with velcro that attaches to the rope and spins, pulling the robot upwards.

That CIM Motor is hooked up to a VictorSP speed controller, and should directly be controlled by the left thumbstick on the operator's controller.

However, there is a ratcheting mechanism that makes sure the robot can't fall down, which makes the motor unable to turn backwards. Thus, to prevent the motor from burning out, we also need to make sure it can't be set to spin backwards.

Subsystem methods
-----------------

This guide assumes that there already is a Climber subsystem, and to set the climber motor, you simply need to write:

.. code-block:: java

	Climber.setClimberMotor(value);

On how to create this subsystem, see `Creating a subsystem <./../../../subsystems/subsystems.html>`_.

We also assume that OI already has a Joystick named operator, whose left thumbstick thumbstick is axis 1.

On how to use OI, see `OI <./../../../../robotcode/structure/oi.html>`_.

Writing the command
-------------------

Once you create the ClimberContinuous command, you'll get a blank command template like this:

.. code-block:: java

	package org.usfirst.frc.team93.robot.commands;

	import edu.wpi.first.wpilibj.command.Command;

	/**
	 * 
	 */
	public class ClimberContinuous extends Command {

	    public ClimberContinuous() {
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
	 * This command gives the operator manual control of the climber.
	 * This command also prevents turning the climber backwards to prevent burning out the motor.
	 */

Then, in the constructor, since this command uses the climber motor, make sure this command requires the Climber subsystem.

.. code-block:: java

    public ClimberContinuous()
    {
        // Use requires() here to declare subsystem dependencies
        requires(Robot.climber);
    }

If Robot can't be resolved, make sure to hover over the error and import Robot.

There isn't any need to initialize anything, since all input to comes from the operator joystick. Then, in the execute() method, add the logic of the command.

.. code-block:: java

    // Called repeatedly when this Command is scheduled to run
    protected void execute()
    {
        // get joystick value
        double value = OI.operator.getRawAxis(1);

        // make sure it isn't going backwards
        if (value < 0)
        {
            value = 0;
        }

        // set the climber motor to the non-negative joystick value
        Climber.setClimberMotor(value);
    }

If Climber isn't resolved, make sure to import it.

We always want the operator to have control over the climber, so this command should never finish. Thus, leave isFinished() as it is.

For safety purposes, when this command has to end(), it should stop the climber. interrupted() should do the same.

.. code-block:: java

    // Called once after isFinished returns true
    protected void end()
    {
        Climber.setClimberMotor(0);
    }

    // Called when another command which requires one or more of the same
    // subsystems is scheduled to run
    protected void interrupted()
    {
        end();
    }

Running the command
-------------------

Now, go into the Climber subsystem and make ClimberContinuous the default command of the Climber subsystem.

.. code-block:: java

	@Override
	public void initDefaultCommand()
	{
	    // This is the only command for this subsystem and it needs to run for all of teleop
	    setDefaultCommand(new ClimberContinuous());
	}

Make sure that ClimberContinuous requires Robot.climber, and that the Climber subsystem is constructed in the Robot class, or else there will be a IllegalUseOfCommandException.

And that's it! Now, the operator has manual control over the climber, and cannot move the climber motor backwards and burn out the motor.

Final code
----------

.. code-block:: java

    package org.usfirst.frc.team93.robot.commands;

    import org.usfirst.frc.team93.robot.Robot;
    import org.usfirst.frc.team93.robot.subsystems.Climber;

    import edu.wpi.first.wpilibj.command.Command;

    /**
     * This command gives the operator manual control of the climber. This command
     * also prevents turning the climber backwards to prevent burning out the motor.
     */
    public class ClimberContinuous extends Command
    {
        
        public ClimberContinuous()
        {
            // Use requires() here to declare subsystem dependencies
            requires(Robot.climber);
        }
        
        // Called just before this Command runs the first time
        @Override
        protected void initialize()
        {
        }
        
        // Called repeatedly when this Command is scheduled to run
        @Override
        protected void execute()
        {
            // get joystick value
            double value = OI.operator.getRawAxis(1);

            // make sure it isn't going backwards
            if (value < 0)
            {
                value = 0;
            }

            // set the climber motor to the non-negative joystick value
            Climber.setClimberMotor(value);
        }
        
        // Make this return true when this Command no longer needs to run execute()
        @Override
        protected boolean isFinished()
        {
            return false;
        }
        
        // Called once after isFinished returns true
        @Override
        protected void end()
        {
            Climber.setClimberMotor(0);
        }
        
        // Called when another command which requires one or more of the same
        // subsystems is scheduled to run
        @Override
        protected void interrupted()
        {
            end();
        }
    }

.. toctree::
	:glob:
	:maxdepth: 10
	:caption: Contents
	
	