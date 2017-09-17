PID Sources
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

A PIDSource is anything that can be read from using pidGet(). The most commonly used one is an Encoder.

**Example on how to use PIDSource:**

Say there are 4 drive wheels on the robot, and there is one encoder per wheel. Then, when measuring how far the robot has traveled, it would be wise to try to use the median of all 4 encoders, so that even if one fails, the other ones can still give an accurate result.

However, a PID Controller can only take in one PIDSource. So, we have to write our own implementation of PIDSource.

Create a new class named DrivePIDSource. This class will take the median of the four encoders.

The first thing you should do with the class is to add "implements PIDSource" to the class declaration, to make the compiler see it as a PIDSource.

.. code-block:: java

    import edu.wpi.first.wpilibj.PIDSource;

    public class DrivePIDSource implements PIDSource
    {
        @Override
        public double pidGet()
        {
            // TODO Auto-generated method stub
            return 0;
        }

        @Override
        public PIDSourceType getPIDSourceType()
        {
            // TODO Auto-generated method stub
            return null;
        }

        @Override
        public void setPIDSourceType(PIDSourceType pidSourceType)
        {
            // TODO Auto-generated method stub

        }
    }

First, we should add the 4 drive encoders we're going to pass in to our constructor.

.. code-block:: java

    import edu.wpi.first.wpilibj.PIDSource;

    public class DrivePIDSource implements PIDSource
    {
        Encoder LEFT_FRONT;
        Encoder LEFT_BACK;
        Encoder RIGHT_FRONT;
        Encoder RIGHT_BACK;
        
        public DrivePIDSource(Encoder LEFT_FRONT, Encoder LEFT_BACK, Encoder RIGHT_FRONT, Encoder RIGHT_BACK)
        {
            this.LEFT_FRONT = LEFT_FRONT;
            this.LEFT_BACK = LEFT_BACK;
            this.RIGHT_FRONT = RIGHT_FRONT;
            this.RIGHT_BACK = RIGHT_BACK;
        }

        @Override
        public double pidGet()
        {
            // TODO Auto-generated method stub
            return 0;
        }

        @Override
        public PIDSourceType getPIDSourceType()
        {
            // TODO Auto-generated method stub
            return null;
        }

        @Override
        public void setPIDSourceType(PIDSourceType pidSourceType)
        {
            // TODO Auto-generated method stub

        }
    }

Next, we should get the methods "getPIDSourceType" and "setPIDSourceType" out of the way.

There are two types of PIDSources:

 - PIDSourceType.kDisplacement
 	This means that the PIDSource measures distance of some kind.

 - PIDSourceType.kRate
 	This means that the PIDSource measure speed of some kind.

In our case, these encoders are measuring distance, and are always measuring distance. So, getPIDSourceType should return PIDSourceType.kDisplacement, and setPIDSourceType should do nothing, since we always want to measure distance.

.. code-block:: java

    @Override
    public PIDSourceType getPIDSourceType()
    {
        return PIDSourceType.kDisplacement;
    }

    @Override
    public void setPIDSourceType(PIDSourceType pidSourceType)
    {
        // do nothing since we always measure distance
    }

Now, is our important implementation of pidGet(). The way to find a median of four numbers is to sort the four values, and then take the two middle values and average them.

.. code-block:: java

    @Override
    public double pidGet()
    {
        // get the four encoder values
        double[] encoderValues =
        {
            LEFT_FRONT.get(),
            LEFT_BACK.get(),
            RIGHT_FRONT.get(),
            RIGHT_BACK.get()
        };

        // sort the four encoder values
        Arrays.sort(encoderValues);

        // take the two middle values and average them
        double median = (encoderValues[1] + encoderValues[2]) * 0.5;

        // return the result to the PID Controller
        return median;
    }

Now, all that's left is to pass our custom PID Source implementation into the PID Controller, in the Drive subsystem.

.. code-block:: java
	
    PIDSource DRIVE_ENCODERS = new DrivePIDSource(DRIVE_LEFT_FRONT_ENCODERS, DRIVE_LEFT_BACK_ENCODERS, DRIVE_RIGHT_FRONT_ENCODERS, DRIVE_RIGHT_BACK_ENCODERS);

    DRIVE_CONTROL = new PIDController(0.015, 0.010, 0, DRIVE_ENCODERS, DRIVE_MOTORS);

Now, whenever the DRIVE_CONTROL PIDController reads from the DRIVE_ENCODERS PIDSource, it will instead take the median of all of the drive encoders, giving it a more robust reading.

This is called implementing PIDSource, where you write your own pidGet(), getPIDSourceType(), and setPIDSourceType(PIDSourceType).

Similar to in PIDOutput, you can put anything you want in pidGet(), as long as it returns some value in the end.

Another useful thing to do is to add a get() method to all PIDSources for quality of life, since most programmers are used to calling get() instead of pidGet().

**Important debugging note:**
Note that PIDControllers run in a separate thread. Thus, if an exception occurs in pidGet(), just like in PIDSource.pidWrite(), the exception is printed but it is NOT rethrown, and then the PIDController disables. This means that other than the RioLog, there is no indication that the exception occurred, and the PID will seem to just stop for no reason. If a PID Controller seems to stop functioning (e.g. motor is stuck at a value, sensor input no longer affects it) then check the RioLog for exceptions that the PIDController thread swallowed.

Examples:

 - `TwoEncoderPIDSource <http://nacsvn.aasd.k12.wi.us/repos/nac-software-2016/robot/trunk/Team93Robot2016/src/org/usfirst/frc/team93/robot/Utilities/TwoEncoderPIDSource.java>`_
    A PID Source that adds two encoder readings.
    
 - `AveragePIDSource <http://nacsvn.aasd.k12.wi.us/repos/nac-software-2017/robot/trunk/Team93Robot2017/src/org/usfirst/frc/team93/robot/utilities/AveragePIDSource.java>`_
    Uses the arithmetic mean of the PIDSources given.
 
 - `MedianPIDSource <http://nacsvn.aasd.k12.wi.us/repos/nac-software-2017/robot/trunk/Team93Robot2017/src/org/usfirst/frc/team93/robot/utilities/MedianPIDSource.java>`_
    Uses the median of the PIDSources given, similar to what was contructed as the example.
 
 - `PIDSourceExtended <http://nacsvn.aasd.k12.wi.us/repos/nac-software-2017/robot/trunk/Team93Robot2017/src/org/usfirst/frc/team93/robot/utilities/PIDSourceExtended.java>`_
    Adds a gain multiplier to a PIDSource.

.. toctree::
	:glob:
	:maxdepth: 10
	:caption: Contents:
	
	