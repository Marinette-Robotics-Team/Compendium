Rotating a Claw both Manually and using Setpoints
=================================================

In this example, there is a rotating claw with a shooter on it.

This example talks about how to use two commands (ClawHoldManualControl and ClawRotateToAngle) to give an operator both manual control over a claw's rotation and the ability to turn a claw to setpoints. Additionally, ClawHoldManualControl is designed so that gravity will not cause the claw to slip downwards over time, hence the "Hold" in its name.

Note that this guide assumes that you understand how to use `PIDControllers <./../../../../robotcode/components/pid/pidcontroller1.html>`_, as well as `PIDSources <./../../../../robotcode/components/pid/pidsource.html>`_ and `PIDOutputs <./../../../../robotcode/components/pid/pidoutput.html>`_.

It also assumes knowledge about Potentiometers.

Design
------

The design of this claw is a rotating claw that has a potentiometer to track its angle of rotation. The claw is rotated by a motor, with a VictorSP speed controller attached.

The claw can rotate to the front or the back, to pick up balls from the front or back. On the claw is also a shooter. Thus, it must be able to rotate to the front, back, or shooting position. Additionally, the claw must be able to go straight up to protect it if not in use.

The operator needs to be able to push the A, B, X, and Y buttons to make the claw rotate to certain setpoints:

 - A: 0°, straight up
 - B: 45°, shooting position
 - X: 105°, front downwards for picking up from the front
 - Y: -105°, back downwards for picking up from the back

However, the claw cannot go past 105° or -105° since then the claw is touching the floor, and going any further will burn out the motor.

By default, however, the claw starts out by being manually controlled by the right thumbstick on the operator's controller. Then, the operator can press a button to rotate the claw to an angle setpoint. When it finishes rotating to an angle setpoint, it gives the operator manual control again. Additionally, if the claw is already rotating to a setpoint, the operator can cancel that setpoint and regain manual control by pressing down on the right stick, which is button 10.

Additionally, gravity should not be able to make the claw slip and rotate downwards. This can be accomplished by telling a PID Controller to hold an angle setpoint, meaning that if gravity pulls the claw down, the PID Controller will move it back up.

Subsystem methods
-----------------

This guide assumes that there already is a Claw subsystem, with a CLAW_MOTOR speed controller.

.. code-block:: java

    Claw.CLAW_MOTOR.set(1.0); // sets claw motor to full power forward

Additionally, there is also a potentiometer that tells the angle of the claw. This potentiometer has already been implemented to give an accurate angle.

.. code-block:: java

	Claw.CLAW_POTENTIOMETER.get() // get the angle of the claw

Lastly, there is a tuned PID Controller that takes input from the potentiometer and outputs to the claw motor.

.. code-block:: java

    CLAW_ANGLE_CONTROLLER = new PIDController(0.0015, 0.0001, 0, CLAW_POTENTIOMETER, CLAW_MOTOR);

If this is confusing, see `PIDControllers <./../../../../robotcode/components/pid/pidcontroller1.html>`_.

On how to create this subsystem, see `Creating a subsystem <./../../../subsystems/subsystems.html>`_.

In this example, there is an operator joystick in OI, and on the operator joystick, the right thumbstick is axis 3, and pressing down the joystick is button 10.

On how to use OI, see `OI <./../../../../robotcode/structure/oi.html>`_.

Writing the commands
--------------------

Firstly, create the files for the two commands: ClawHoldManualControl and ClawRotateToAngle. We're going to start with ClawRotateToAngle.

ClawRotateToAngle
^^^^^^^^^^^^^^^^^

This command is designed to rotate the claw to a given angle setpoint, using a PID Controller. It will immediately end and do nothing if you give an angle outside of its range of -105° to 105°, for safety reasons.

.. code-block:: java

	package org.usfirst.frc.team93.robot.commands;

	import edu.wpi.first.wpilibj.command.Command;

	/**
	 * 
	 */
	public class ClawRotateToAngle extends Command {

	    public ClawRotateToAngle() {
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
     * This command rotates the claw to an angle setpoint, using a PID Controller.
     * It will immediately end and do nothing if you give an angle outside of its range of -105 to 105, for safety reasons.
     */

In the constructor, pass in an angle setpoint and a maxError. Additionally, since this command uses the claw, make sure this command requires the Claw subsystem.

.. code-block:: java
	
    double m_setpoint;
    double m_maxError;

    public ClawRotateToAngle(double setpoint, double maxError)
    {
        m_setpoint = setpoint;
        m_maxError = maxError;

        // Use requires() here to declare subsystem dependencies
        requires(Robot.claw);
    }

Then, in initialize, do the normal PID Controller routine of resetting the controller, setting the setpoint, setting the error tolerance, and enabling it. Note that this time, we are NOT resetting the sensor, since we want absolute position (e.g. rotate to 90°) instead of relative position (e.g. drive forwards 10 feet). Additionally, resetting a potentiometer doesn't make much sense anyway, since it is already an absolute position sensor.

.. code-block:: java

    // Called just before this Command runs the first time
    @Override
    protected void initialize()
    {
        Claw.CLAW_ANGLE_CONTROLLER.reset();
        Claw.CLAW_ANGLE_CONTROLLER.setSetpoint(m_setpoint);
        Claw.CLAW_ANGLE_CONTROLLER.setAbsoluteTolerance(m_maxError);
        Claw.CLAW_ANGLE_CONTROLLER.enable();
    }

The execute() method can be left empty since the PID Controller does all of the work here.

For isFinished(), we want to end the command immediately if the angle setpoint isn't within the valid range of -105° to 105°. Additionally, if the PID is on target, we want to end the command also.

But, remember that the operator can regain manual control by pressing the right thumbstick. So, when the button 10 is pressed down, then end the command also, reverting to the subsystem's default command of manual control.

.. code-block:: java

    // Make this return true when this Command no longer needs to run execute()
    @Override
    protected boolean isFinished()
    {
        return (m_setpoint < -105.0 || m_setpoint > 105.0 || Claw.CLAW_ANGLE_CONTROLLER.onTarget() || OI.operator.getRawButton(10));
    }

Lastly, for end() and interrupted(), we simply want to disable the PID Controller and stop the claw motor.

.. code-block:: java

    // Called once after isFinished returns true
    protected void end()
    {
        Claw.CLAW_ANGLE_CONTROLLER.disable();
        Claw.CLAW_MOTOR.set(0);
    }

    // Called when another command which requires one or more of the same
    // subsystems is scheduled to run
    protected void interrupted()
    {
        end();
    }

And that's that for the ClawRotateToAngle command. Time to move on to ClawHoldManualControl.

ClawHoldManualControl
^^^^^^^^^^^^^^^^^^^^^

This is going to be the default command of the Claw subsystem.

This command is designed to run when the operator is manually controlling the claw with the right thumbstick. It simply gives the operator manual control, where the claw moves forward when the joystick moves up, and vice versa. However, the operator cannot rotate the claw forwards past 105° or backwards past -105° for safety reasons.

Additionally, however, when the operator's thumbstick is at 0, then a PID Controller will enable. This PID Controller holds the angle the claw had when the operator released the thumbstick, which means that the claw won't slip downward over time due to gravity. When the operator moves the thumbstick, the PID Controller will disable again.

In the javadoc at the top, describe what this command does.

.. code-block:: java

    /**
     * This command gives the operator manual control of the claw rotation using the right thumbstick.
     * Additionally, if the operator has released the thumbstick, a PID Controller will make sure the claw does not slip downwards due to gravity.
     */

In the constructor, since this command uses the claw, make sure this command requires the Claw subsystem. But, also make sure to add a boolean called *thumbstickReleased* in the class that we'll use in execute().

.. code-block:: java
    
    boolean thumbstickReleased = false;

    public ClawHoldManualControl()
    {
        // Use requires() here to declare subsystem dependencies
        requires(Robot.claw);
    }

If Robot can't be resolved, make sure to hover over the error and import Robot.

Then, since all of our input comes from the joystick, we don't need anything in initialize(), so move on to execute().

In execute(), we want the claw to move forward when the right thumbstick presses up, and backwards when it presses down. That means that we should set our motor to the operator's thumbstick value.

However, if the potentiometer is at 105° and the operator tries to rotate the claw forwards, we should set the motor to 0. Similarly, if the potentiometer is at -105° and the operator tries to rotate the claw backwards, we set the motor to 0 as well.

Then, to hold the position when the operator releases the thumbstick, enable the PID Controller to hold the current potentiometer reading if the operator releases the thumbstick. However, if the operator moves the thumbstick again, disable that PID Controller.

.. code-block:: java

    // Called repeatedly when this Command is scheduled to run
    protected void execute()
    {
        // get deadzoned joystick value
        double value = OI.deadzone(OI.operator.getRawAxis(3), 0.1);

        // set claw motor to joystick value initially
        Claw.CLAW_MOTOR.set(value);

        // but, if the claw is rotating forwards, and the potentiometer is over 105
        if (value > 0 && Claw.CLAW_POTENTIOMETER.get() >= 105.0)
        {
            // set the motor to 0
            Claw.CLAW_MOTOR.set(0);
        }

        // but, if the elevator is rotating backwards, and the potentiometer is under -105
        if (value < 0 && Claw.CLAW_POTENTIOMETER.get() <= -105.0)
        {
            // set the motor to 0
            Claw.CLAW_MOTOR.set(0);
        }

        // if the operator released the thumbstick (if it's close to 0)
        if (value == 0)
        {
            // if it wasn't released before
            if (!thumbstickReleased)
            {
                // set the PID Controller to hold this current position
                Claw.CLAW_ANGLE_CONTROLLER.reset();
                Claw.CLAW_ANGLE_CONTROLLER.setSetpoint(Claw.CLAW_POTENTIOMETER.get());
                Claw.CLAW_ANGLE_CONTROLLER.enable();
            }

            // thumbstick was released
            thumbstickReleased = true;
        }
        // if the operator moves the thumbstick
        else
        {
            // disable the PID Controller holding the current position since the operator is in control now
            Claw.CLAW_ANGLE_CONTROLLER.disable();

            // thumbstick is no longer released
            thumbstickReleased = false;
        }
    }

If Claw isn't resolved, make sure to import it.

There should be no reason for this command to end on its own without being interrupted, since that means that the operator loses control over the claw. Thus, leave isFinished as it is.

For safety purposes, when this command has to end(), it should stop the claw motor, and disable the PID Controller. interrupted() should do the same.

.. code-block:: java

    // Called once after isFinished returns true
    protected void end()
    {
        Claw.CLAW_ANGLE_CONTROLLER.disable();
        Claw.CLAW_MOTOR.set(0);
    }

    // Called when another command which requires one or more of the same
    // subsystems is scheduled to run
    protected void interrupted()
    {
        end();
    }

Now, the ClawHoldManualControl command is done.

Running the command
-------------------

Firstly, make the ClawHoldManualControl command the default command of the Claw subsystem.

.. code-block:: java

	@Override
	public void initDefaultCommand()
	{
	    setDefaultCommand(new ClawHoldManualControl());
	}

Then, bind each of the operator buttons to the correct ClawRotateToAngle commands in OI's constructor.

.. code-block:: java
    :emphasize-lines: 9,10,10,13,15,16,18,19

    public static Joystick driver;
    public static Joystick operator;

    public OI()
    {
        driver = new Joystick(0);
        operator = new Joystick(1);

        Button clawUpButton = new JoystickButton(operator, 1);     // A button
        clawUpButton.whenPressed(new ClawRotateToAngle(0.0));      // claw straight up

        Button clawShootButton = new JoystickButton(operator, 2);  // B button
        clawShootButton.whenPressed(new ClawRotateToAngle(45.0));  // shooting position

        Button clawFrontButton = new JoystickButton(operator, 3);  // X button
        clawFrontButton.whenPressed(new ClawRotateToAngle(105.0)); // front downwards

        Button clawBackButton = new JoystickButton(operator, 4);   // Y button
        clawBackButton.whenPressed(new ClawRotateToAngle(-105.0)); // back downwards
    }

Final code
----------

ClawRotateToAngle
^^^^^^^^^^^^^^^^^

.. code-block:: java

    package org.usfirst.frc.team93.robot.commands;

    import org.usfirst.frc.team93.robot.Robot;
    import org.usfirst.frc.team93.robot.subsystems.Claw;

    import edu.wpi.first.wpilibj.command.Command;

    /**
     * This command rotates the claw to an angle setpoint, using a PID Controller.
     * It will immediately end and do nothing if you give an angle outside of its range of -105 to 105, for safety reasons.
     */
    public class ClawRotateToAngle extends Command
    {
        double m_setpoint;
        double m_maxError;

        public ClawRotateToAngle(double setpoint, double maxError)
        {
            m_setpoint = setpoint;
            m_maxError = maxError;

            // Use requires() here to declare subsystem dependencies
            requires(Robot.claw);
        }
        
        // Called just before this Command runs the first time
        @Override
        protected void initialize()
        {
            Claw.CLAW_ANGLE_CONTROLLER.reset();
            Claw.CLAW_ANGLE_CONTROLLER.setSetpoint(m_setpoint);
            Claw.CLAW_ANGLE_CONTROLLER.setAbsoluteTolerance(m_maxError);
            Claw.CLAW_ANGLE_CONTROLLER.enable();
        }
        
        // Called repeatedly when this Command is scheduled to run
        protected void execute()
        {
        }
        
        // Make this return true when this Command no longer needs to run execute()
        @Override
        protected boolean isFinished()
        {
            return (m_setpoint < -105.0 || m_setpoint > 105.0 || Claw.CLAW_ANGLE_CONTROLLER.onTarget() || OI.operator.getRawButton(10));
        }
    
        // Called once after isFinished returns true
        protected void end()
        {
            Claw.CLAW_ANGLE_CONTROLLER.disable();
            Claw.CLAW_MOTOR.set(0);
        }

        // Called when another command which requires one or more of the same
        // subsystems is scheduled to run
        protected void interrupted()
        {
            end();
        }
    }

ClawHoldManualControl
^^^^^^^^^^^^^^^^^^^^^

.. code-block:: java

    package org.usfirst.frc.team93.robot.commands;

    import org.usfirst.frc.team93.robot.Robot;
    import org.usfirst.frc.team93.robot.subsystems.Claw;

    import edu.wpi.first.wpilibj.command.Command;

    /**
     * This command gives the operator manual control of the claw rotation using the right thumbstick.
     * Additionally, if the operator has released the thumbstick, a PID Controller will make sure the claw does not slip downwards due to gravity.
     */
    public class ClawHoldManualControl extends Command
    {
        boolean thumbstickReleased;

        public ClawHoldManualControl()
        {
            // Use requires() here to declare subsystem dependencies
            requires(Robot.claw);
        }
        
        // Called just before this Command runs the first time
        @Override
        protected void initialize()
        {
        }
        
        // Called repeatedly when this Command is scheduled to run
        protected void execute()
        {
            // get deadzoned joystick value
            double value = OI.deadzone(OI.operator.getRawAxis(3), 0.1);

            // set claw motor to joystick value initially
            Claw.CLAW_MOTOR.set(value);

            // but, if the claw is rotating forwards, and the potentiometer is over 105
            if (value > 0 && Claw.CLAW_POTENTIOMETER.get() >= 105.0)
            {
                // set the motor to 0
                Claw.CLAW_MOTOR.set(0);
            }

            // but, if the elevator is rotating backwards, and the potentiometer is under -105
            if (value < 0 && Claw.CLAW_POTENTIOMETER.get() <= -105.0)
            {
                // set the motor to 0
                Claw.CLAW_MOTOR.set(0);
            }

            // if the operator released the thumbstick (if it's close to 0)
            if (value == 0)
            {
                // if it wasn't released before
                if (!thumbstickReleased)
                {
                    // set the PID Controller to hold this current position
                    Claw.CLAW_ANGLE_CONTROLLER.reset();
                    Claw.CLAW_ANGLE_CONTROLLER.setSetpoint(Claw.CLAW_POTENTIOMETER.get());
                    Claw.CLAW_ANGLE_CONTROLLER.enable();
                }

                // thumbstick was released
                thumbstickReleased = true;
            }
            // if the operator moves the thumbstick
            else
            {
                // disable the PID Controller holding the current position since the operator is in control now
                Claw.CLAW_ANGLE_CONTROLLER.disable();

                // thumbstick is no longer released
                thumbstickReleased = false;
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
            Claw.CLAW_ANGLE_CONTROLLER.disable();
            Claw.CLAW_MOTOR.set(0);
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
	
	