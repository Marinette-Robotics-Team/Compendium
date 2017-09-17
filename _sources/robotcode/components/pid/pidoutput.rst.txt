PID Outputs
===========

Note: This guide is mainly for advanced users. For basic use, see `PIDControllers <pidcontroller1.html>`_.
This guide also requires knowledge about how to use Interfaces.

Remember that a PIDController's basic structure is kind of like this:

.. code-block:: java

    while (enabled)
    {
        // get sensor reading
        double sensor = source.pidGet();
		
        // calculate what to write to the output using the input and setpoint
        double value = calculateOutput(setpoint, sensor);
		
        // write to the output
        output.pidWrite(value);
    }

Now, a PIDOutput is simply something that can be written to using pidWrite. The most common one is a speed controller.

**Example on how to use PIDOutput:**

When getting the robot to drive a certain distance forwards, usually, the drive PID Controller should write the same value to all of the drive motors. However, you can only supply one PIDOutput object as an output for a PIDController. How, then would we get one PID Controller to write to all 4 drive motors?

In this case, it would be wise to write your own custom implementation of PIDOutput.

Create a new class named DrivePIDOutput. This class will group together several PID Outputs.

The first thing you should do with the class is to add "implements PIDOutput" to the class declaration, to make the compiler see it as a PIDOutput.

.. code-block:: java

    import edu.wpi.first.wpilibj.PIDOutput;

    public class DrivePIDOutput implements PIDOutput
    {
        @Override
        public void pidWrite(double output)
        {
            // TODO Auto-generated method stub
            
        }
    }

Then, the class needs to know what the speed controllers are. Add these to the method's class, and pass them into the constructor.

.. code-block:: java

    import edu.wpi.first.wpilibj.PIDOutput;

    public class DrivePIDOutput implements PIDOutput
    {
        SpeedController LEFT_FRONT;
        SpeedController LEFT_BACK;
        SpeedController RIGHT_FRONT;
        SpeedController RIGHT_BACK;
        
        public DrivePIDOutput(SpeedController LEFT_FRONT, SpeedController LEFT_BACK, SpeedController RIGHT_FRONT, SpeedController RIGHT_BACK)
        {
            this.LEFT_FRONT = LEFT_FRONT;
            this.LEFT_BACK = LEFT_BACK;
            this.RIGHT_FRONT = RIGHT_FRONT;
            this.RIGHT_BACK = RIGHT_BACK;
        }
		
        @Override
        public void pidWrite(double output)
        {
            // TODO Auto-generated method stub
            
        }
    }

Now, the important part is that whenever the PID Controller calls pidWrite(), we want it to write to all of the drive motors.

.. code-block:: java

    @Override
    public void pidWrite(double output)
    {
        // write to all of the drive motors
        LEFT_FRONT.pidWrite(output);
        LEFT_BACK.pidWrite(output);
        RIGHT_FRONT.pidWrite(output);
        RIGHT_BACK.pidWrite(output);
    }

And that's it! Now just make sure to, in the Drive subsystem, have:

.. code-block:: java

    PIDOutput DRIVE_MOTORS = new DrivePIDOutput(DRIVE_LEFT_FRONT, DRIVE_LEFT_BACK, DRIVE_RIGHT_FRONT, DRIVE_RIGHT_BACK);

    DRIVE_CONTROL = new PIDController(0.015, 0.010, 0, DRIVE_ENCODER, DRIVE_MOTORS);

Now, whenever the PID Controller DRIVE_CONTROL writes to DRIVE_MOTORS, then it will also write to all of the drive motors, giving us the behavior we want.

This is called implementing PIDOutput, where you put your own code in pidWrite(), and then the PIDController will call that code.

Note that you can put anything you want in pidWrite(), from setting a variable, to printing out text. It doesn't have to set a motor.

In this example, we grouped 4 motors together. However, note that you can make groups of 2, 3, or even any size. You can also add the ability to multiply a motor's output by a certain value, called a gain, and more.

Another useful thing to do is to add a set method to all PIDOutputs for quality of life, since most programmers are used to calling set instead of pidWrite.

**Important debugging note:**
Note that PIDControllers run in a separate thread. Thus, if an exception occurs in pidWrite(), just like in PIDSource.pidGet(), the exception is printed but it is NOT rethrown, and then the PIDController disables. This means that other than the RioLog, there is no indication that the exception occurred, and the PID will seem to just stop for no reason. If a PID Controller seems to stop functioning (e.g. motor is stuck at a value, sensor input no longer affects it) then check the RioLog for exceptions that the PIDController thread swallowed.

Examples:

 - `PrintPIDOutput <http://nacsvn.aasd.k12.wi.us/repos/nac-software-2017/robot/trunk/Team93Robot2017/src/org/usfirst/frc/team93/robot/utilities/PrintPIDOutput.java>`_
    Simply prints out everything that is written to it.
    
 - `PIDOutputGroup <http://nacsvn.aasd.k12.wi.us/repos/nac-software-2017/robot/trunk/Team93Robot2017/src/org/usfirst/frc/team93/robot/utilities/PIDOutputGroup.java>`_
    Groups any number of PIDOutputs together.
 
 - `PIDOutputExtended <http://nacsvn.aasd.k12.wi.us/repos/nac-software-2017/robot/trunk/Team93Robot2017/src/org/usfirst/frc/team93/robot/utilities/PIDOutputExtended.java>`_
    Adds a gain multiplier to a PIDOutput.
 
 - `SteeringAndSpeedCoordinator <http://nacsvn.aasd.k12.wi.us/repos/nac-software-2016/robot/trunk/Team93Robot2016/src/org/usfirst/frc/team93/robot/Utilities/SteeringAndSpeedCoordinator.java>`_
    Uses PIDOutputs to receive information from the PIDController about how to control both speed and the robot's heading.
    
.. toctree::
	:glob:
	:maxdepth: 10
	:caption: Contents:
	
	