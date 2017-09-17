Tank Drive
==========

This example describes a basic tank drive, that takes joystick input directly to drive motor output. It does NOT include more advanced things that may also go in a tank drive, such as joystick input curves. For more information on drive systems, see .

Design
------

The design of this drive platform is that it is a simple tank drive. There are 3 motors for each side of the robot, and each motor is controlled by a VictorSP speed controller.

Subsystem methods
-----------------

This guide assumes that there already is a Drive subsystem, and to set the drive motors, you simply need to write:

.. code-block:: java

	Drive.LEFT_DRIVE_MOTORS.set(1.0);  // left
	Drive.RIGHT_DRIVE_MOTORS.set(1.0); // right

On how to create this subsystem, see `Creating a subsystem <./../../../subsystems/subsystems.html>`_.

We also assume that OI already has a Joystick named driver, whose left thumbstick thumbstick's y axis is axis 1 and right thumbstick's y axis is axis 3.

On how to use OI, see `OI <./../../../../robotcode/structure/oi.html>`_.

Writing the command
-------------------

Once you create the DriveContinuous command, you'll get a blank command template like this:

.. code-block:: java

	package org.usfirst.frc.team93.robot.commands;

	import edu.wpi.first.wpilibj.command.Command;

	/**
	 * 
	 */
	public class DriveContinuous extends Command {

	    public DriveContinuous() {
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
     * This command gives the driver a tank drive control.
     * Pushing forwards on the left stick makes the left wheels go forwards,
     * and pushing forwards on the right stick makes the right wheels go forwards.
     */

Then, in the constructor, since this command uses the drive motors, make sure this command requires the Drive subsystem.

.. code-block:: java

    public DriveContinuous()
    {
        // Use requires() here to declare subsystem dependencies
        requires(Robot.drive);
    }

If Robot can't be resolved, make sure to hover over the error and import Robot.

There isn't any need to initialize anything, since all input to comes from the driver joystick. Then, in the execute() method, add the logic of the command.

Now, since we want to set the power of the motors to the thumbstick input, we use OI.driver.getRawAxis(axis) to get the thumbstick values, and then set the drive motors for each side to those values.

.. code-block:: java

    // Called repeatedly when this Command is scheduled to run
    protected void execute()
    {
        // set drive motors to joystick values
        Drive.LEFT_DRIVE_MOTORS.set(OI.driver.getRawAxis(1));
        Drive.RIGHT_DRIVE_MOTORS.set(OI.driver.getRawAxis(3));
    }

If Drive isn't resolved, make sure to import it.

We always want the driver to have control over the robot, so this command should never finish. Thus, leave isFinished() as it is.

Firstly, the PID Controller has to be disabled, otherwise the robot will keep trying to go to the setpoint even after the command ends. Then, for safety purposes, when this command has to end(), it should stop the robot also. interrupted() should do the same.

.. code-block:: java

    // Called once after isFinished returns true
    protected void end()
    {
        Drive.DRIVE_DISTANCE_CONTROLLER.disable();
        Drive.LEFT_DRIVE_MOTORS.set(0);
        Drive.RIGHT_DRIVE_MOTORS.set(0);
    }

    // Called when another command which requires one or more of the same
    // subsystems is scheduled to run
    protected void interrupted()
    {
        end();
    }

Running the command
-------------------

Now, go into the Drive subsystem and make DriveContinuous the default command of the Drive subsystem.

.. code-block:: java

	@Override
	public void initDefaultCommand()
	{
	    // This is the only command for this subsystem and it needs to run for all of teleop
	    setDefaultCommand(new DriveContinuous());
	}

Make sure that DriveContinuous requires Robot.drive, and that the Drive subsystem is constructed in the Robot class, or else there will be a IllegalUseOfCommandException.

And that's it! Now, the driver has tank drive control over the robot.

Final code
----------

.. code-block:: java

    package org.usfirst.frc.team93.robot.commands;

    import org.usfirst.frc.team93.robot.Robot;
    import org.usfirst.frc.team93.robot.subsystems.Drive;

    import edu.wpi.first.wpilibj.command.Command;

    /**
     * This command gives the driver a tank drive control.
     * Pushing forwards on the left stick makes the left wheels go forwards,
     * and pushing forwards on the right stick makes the right wheels go forwards.
     */
    public class DriveContinuous extends Command
    {
        
        public DriveContinuous()
        {
            // Use requires() here to declare subsystem dependencies
            requires(Robot.drive);
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
            // set drive motors to joystick values
            Drive.LEFT_DRIVE_MOTORS.set(OI.driver.getRawAxis(1));
            Drive.RIGHT_DRIVE_MOTORS.set(OI.driver.getRawAxis(3));
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
            Drive.LEFT_DRIVE_MOTORS.set(0);
            Drive.RIGHT_DRIVE_MOTORS.set(0);
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
	
	