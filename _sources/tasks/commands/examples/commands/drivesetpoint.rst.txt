Driving a Distance Forward
==========================

This example talks about how to use a PID Controller and Encoders to make the robot accurately drive a certain distance forward.

Note that this guide assumes that you understand how to use `PIDControllers <./../../../../robotcode/components/pid/pidcontroller1.html>`_, as well as `PIDSources <./../../../../robotcode/components/pid/pidsource.html>`_ and `PIDOutputs <./../../../../robotcode/components/pid/pidoutput.html>`_.

Design
------

The design of this drive is simply a normal tank drive. There are three motors on the left, and three motors on the right, and every motor has its own speed controller. Additionally, there is one encoder per side of the robot.

Subsystem methods
-----------------

This guide assumes that there already is a Drive subsystem, with PIDOutputs that group together the motors. In this example, we mainly care about the ALL_MOTORS group, that, when written to, sets all of the drive motors to the same value. If this is confusing, see `PIDOutputs <./../../../../robotcode/components/pid/pidoutput.html>`_.

.. code-block:: java

    Drive.DRIVE_ALL_MOTORS.set(1.0); // sets all motors to full power forward

Additionally, there is already a PIDSource that groups together the drive encoders, by taking the average of the left and right drive encoders. If this is confusing, see `PIDSources <./../../../../robotcode/components/pid/pidsource.html>`_.

.. code-block:: java

	Drive.DRIVE_ENCODERS.get() // get the average of the left and right encoders

Lastly, there is a PID Controller that takes input from the encoders and outputs to the drive motors.

.. code-block:: java

    DRIVE_DISTANCE_CONTROLLER = new PIDController(0.015, 0.0010, 0, DRIVE_ENCODERS, DRIVE_ALL_MOTORS);

If this is confusing, see `PIDControllers <./../../../../robotcode/components/pid/pidcontroller1.html>`_.

On how to create this subsystem, see `Creating a subsystem <./../../../subsystems/subsystems.html>`_.

Writing the command
-------------------

Once you create the DriveForwards command, you'll get a blank command template like this:

.. code-block:: java

	package org.usfirst.frc.team93.robot.commands;

	import edu.wpi.first.wpilibj.command.Command;

	/**
	 * 
	 */
	public class DriveForwards extends Command {

	    public DriveForwards() {
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
     * This command moves the robot a certain distance forwards.
     */

Firstly, we have to pass in a distance that we want the robot to move. Then, we want to pass in a certain distance called a *maxError*. When the robot is within the maxError to the distance we want it to move, then command will end. This is necessary because the robot almost never truly reaches its target. It ends up just a little bit off, and the code needs to know how much accuracy is good enough, or else the command will never end, and it will stay at a fraction of an encoder tick off of the target forever.

Additionally, in the constructor, since this command runs on the drive, make sure this command requires the Drive subsystem.

.. code-block:: java
    
    double m_distance;
    double m_maxError;

    public DriveForwards(double distance, double maxError)
    {
        m_distance = distance;
        m_maxError = maxError;

        // Use requires() here to declare subsystem dependencies
        requires(Robot.drive);
    }

If Robot can't be resolved, make sure to hover over the error and import Robot.

Then, when the command starts, we reset the encoders, reset the PID Controller, give the PID Controller a distance setpoint and a maxError, and then enable it.

We reset the encoders because if we want the robot to move 1000.0 ticks forward, if the encoders are already at a nonzero value, the robot will not move 1000.0 ticks. Thus, we want to reset the encoders back to 0.0.

We reset the PID Controller because we don't want any previously accumulated I value to leak into this command run. 

Giving the PID Controller a setpoint and a maxError, as well as enabling it, are self-explanatory.

.. code-block:: java

    // Called just before this Command runs the first time
    @Override
    protected void initialize()
    {
        Drive.DRIVE_ENCODERS.reset();
        Drive.DRIVE_DISTANCE_CONTROLLER.reset();
        Drive.DRIVE_DISTANCE_CONTROLLER.setSetpoint(m_distance);
        Drive.DRIVE_DISTANCE_CONTROLLER.setAbsoluteTolerance(m_maxError);
        Drive.DRIVE_DISTANCE_CONTROLLER.enable();
    }

Now, since we've enabled the PID controller, the PID controller will now do all the work, instead of the command. Thus, the execute() method should actually be empty, so skip to the isFinished() method.

Now, the command should finish when the PID is within its error tolerance. We check this using the PIDController's onTarget() method.

.. code-block:: java

    // Make this return true when this Command no longer needs to run execute()
    @Override
    protected boolean isFinished()
    {
        // end when the PID Controller is within its error tolerance
        return Drive.DRIVE_DISTANCE_CONTROLLER.onTarget();
    }

For safety purposes, when this command has to end(), it should stop the drive motors. interrupted() should do the same.

.. code-block:: java

    // Called once after isFinished returns true
    protected void end()
    {
        Drive.DRIVE_ALL_MOTORS.set(0);
    }

    // Called when another command which requires one or more of the same
    // subsystems is scheduled to run
    protected void interrupted()
    {
        end();
    }

Running the command
-------------------

This command can be run in a multitude of ways. It can be placed in an autonomous plan, which is the most common usage, but it can be bound to a Joystick Button if the driver needs a way to back up straight, for example. There are many different ways to use a DriveForwards command, each with its own way of running it. See `Running the command <./../../commands.html#running-the-command>`_.

Final code
----------

.. code-block:: java

    package org.usfirst.frc.team93.robot.commands;

    import org.usfirst.frc.team93.robot.Robot;
    import org.usfirst.frc.team93.robot.subsystems.Drive;

    import edu.wpi.first.wpilibj.command.Command;

    /**
     * This command moves the robot a certain distance forwards.
     */
    public class DriveForwards extends Command
    {
        double m_distance;
        double m_maxError;

        public DriveForwards(double distance, double maxError)
        {
            m_distance = distance;
            m_maxError = maxError;

            // Use requires() here to declare subsystem dependencies
            requires(Robot.drive);
        }
        
        // Called just before this Command runs the first time
        @Override
        protected void initialize()
        {
            Drive.DRIVE_ENCODERS.reset();
            Drive.DRIVE_DISTANCE_CONTROLLER.reset();
            Drive.DRIVE_DISTANCE_CONTROLLER.setSetpoint(m_distance);
            Drive.DRIVE_DISTANCE_CONTROLLER.setAbsoluteTolerance(m_maxError);
            Drive.DRIVE_DISTANCE_CONTROLLER.enable();
        }
        
        // Called repeatedly when this Command is scheduled to run
        protected void execute()
        {
        }
        
        // Make this return true when this Command no longer needs to run execute()
        @Override
        protected boolean isFinished()
        {
            return Drive.DRIVE_DISTANCE_CONTROLLER.onTarget();
        }
    
        // Called once after isFinished returns true
        protected void end()
        {
            Drive.DRIVE_ALL_MOTORS.set(0);
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
	
	