Tank Drive subsystem
====================

In this example, we will create a fairly standard tank drive subystem, present in almost any robot that has tank drive.

This example will use a 6 motor drive train, with 3 motors on each side, controlled by Talon SRX's (CANTalon's). The subsystem will also have a group that set the right motors and a group that sets the left motors, as well as a group that sets all of the motors.

Additionally, there are 2 encoders, one for each side. These will also be made into a group that averages the two.

There is also a gyro, which tracks the rotation of the robot.

Lastly, there will be a PID controller that controls the robot's distance using the encoders, as well as a PID controller that controls the robot's angle using the gyro.

Start out by `creating the subsystem file <./../subsystems.html#create-the-subsystem-s-file>`_. You'll start out with this code:

.. code-block:: java

    /**
     * The drive subsystem.
     */
    public class Drive extends Subsystem {

        // Put methods for controlling this subsystem
        // here. Call these from Commands.

        public Drive()
        {

        }

        public void initDefaultCommand() {
            // Set the default command for a subsystem here.
            //setDefaultCommand(new MySpecialCommand());
        }
    }

Motors/Speed Controllers
^^^^^^^^^^^^^^^^^^^^^^^^

Now, create the Talon SRX's (CANTalon's) that represent the motors.

Declare them in the class:

.. code-block:: java
	
	// Left Motors
	private static CANTalon LEFT_FRONT_DRIVE_MOTOR;
	private static CANTalon LEFT_BACK_DRIVE_MOTOR;
	private static CANTalon LEFT_TOP_DRIVE_MOTOR;

	// Right Motors
	private static CANTalon RIGHT_FRONT_DRIVE_MOTOR;
	private static CANTalon RIGHT_BACK_DRIVE_MOTOR;
	private static CANTalon RIGHT_TOP_DRIVE_MOTOR;

and initialize them in the constructor:

.. code-block:: java
   :emphasize-lines: 3,4,5,6,8,9,10,11

    public Drive()
    {
        // Left Motors
        LEFT_FRONT_DRIVE_MOTOR = new CANTalon(RobotMap.LEFT_FRONT_DRIVE_MOTOR_PIN);
        LEFT_BACK_DRIVE_MOTOR = new CANTalon(RobotMap.LEFT_BACK_DRIVE_MOTOR_PIN);
        LEFT_TOP_DRIVE_MOTOR = new CANTalon(RobotMap.LEFT_TOP_DRIVE_MOTOR_PIN);

        // Right Motors
        RIGHT_FRONT_DRIVE_MOTOR = new CANTalon(RobotMap.RIGHT_FRONT_DRIVE_MOTOR_PIN);
        RIGHT_BACK_DRIVE_MOTOR = new CANTalon(RobotMap.RIGHT_BACK_DRIVE_MOTOR_PIN);
        RIGHT_TOP_DRIVE_MOTOR = new CANTalon(RobotMap.RIGHT_TOP_DRIVE_MOTOR_PIN);
    }

Next, group the motors. This can be done by either `writing your own custom PIDOutput <./../../../robotcode/components/pid/pidoutput.html>`_ that serves as a PIDOutputGroup, or using `a pre-existing PIDOutputGroup class <http://nacsvn.aasd.k12.wi.us/repos/nac-software-2017/robot/trunk/Team93Robot2017/src/org/usfirst/frc/team93/robot/utilities/PIDOutputGroup.java>`_ (why reinvent the wheel?).

Add the PIDOutputGroups to the class:

.. code-block:: java
	
	// PID Output Groups
	public static PIDOutputGroup LEFT_DRIVE_MOTORS;
	public static PIDOutputGroup RIGHT_DRIVE_MOTORS;
	public static PIDOutputGroup ALL_DRIVE_MOTORS;

Then initialize those PIDOutputGroups in the constructor:

.. code-block:: java
    :emphasize-lines: 13,14,15,16

    public Drive()
    {
        // Left Motors
        LEFT_FRONT_DRIVE_MOTOR = new CANTalon(RobotMap.LEFT_FRONT_DRIVE_MOTOR_PIN);
        LEFT_BACK_DRIVE_MOTOR = new CANTalon(RobotMap.LEFT_BACK_DRIVE_MOTOR_PIN);
        LEFT_TOP_DRIVE_MOTOR = new CANTalon(RobotMap.LEFT_TOP_DRIVE_MOTOR_PIN);

        // Right Motors
        RIGHT_FRONT_DRIVE_MOTOR = new CANTalon(RobotMap.RIGHT_FRONT_DRIVE_MOTOR_PIN);
        RIGHT_BACK_DRIVE_MOTOR = new CANTalon(RobotMap.RIGHT_BACK_DRIVE_MOTOR_PIN);
        RIGHT_TOP_DRIVE_MOTOR = new CANTalon(RobotMap.RIGHT_TOP_DRIVE_MOTOR_PIN);

        // PID Output Groups
        LEFT_DRIVE_MOTORS = new PIDOutputGroup(LEFT_FRONT_DRIVE_MOTOR, LEFT_BACK_DRIVE_MOTOR, LEFT_TOP_DRIVE_MOTOR);
        RIGHT_DRIVE_MOTORS = new PIDOutputGroup(RIGHT_FRONT_DRIVE_MOTOR, RIGHT_BACK_DRIVE_MOTOR, RIGHT_TOP_DRIVE_MOTOR);
        ALL_DRIVE_MOTORS = new PIDOutputGroup(LEFT_DRIVE_MOTORS, RIGHT_DRIVE_MOTORS);
    }

Now, add the subsystem methods for controlling the motors. These are fairly simple. For example, in setLeftMotors, one would put LEFT_DRIVE_MOTORS.set(value).

.. code-block:: java
	
	/**
	 * Sets the left drive motors.
	 */
	public static void setLeftMotors(double value)
	{
	    LEFT_DRIVE_MOTORS.set(value);
	}

	/**
	 * Sets the right drive motors.
	 */
	public static void setRightMotors(double value)
	{
	    RIGHT_DRIVE_MOTORS.set(value);
	}

	/**
	 * Sets all of the drive motors.
	 */
	public static void setAllMotors(double value)
	{
	    ALL_DRIVE_MOTORS.set(value);
	}

Encoders
^^^^^^^^

Now that all the motors are in the subsystem, add the sensors. Starting with the encoders, add them to the class and the constructor.

Also add a PIDSourceGroup that groups together the two encoders. You can either `create this custom PIDSourceGroup yourself <./../../../robotcode/components/pid/pidsource.html>`_ or `use one that already exists <http://nacsvn.aasd.k12.wi.us/repos/nac-software-2017/robot/trunk/Team93Robot2017/src/org/usfirst/frc/team93/robot/utilities/MedianPIDSource.java>`_ (again, why reinvent the wheel?).

Class:

.. code-block:: java
	
	// Encoders
	public static Encoder LEFT_DRIVE_ENCODER;
	public static Encoder RIGHT_DRIVE_ENCODER;
	public static MedianPIDSource DRIVE_ENCODERS;

Constructor:

.. code-block:: java
    :emphasize-lines: 18,19,20,21

    public Drive()
    {
        // left motors
        LEFT_FRONT_DRIVE_MOTOR = new CANTalon(RobotMap.LEFT_FRONT_DRIVE_MOTOR_PIN);
        LEFT_BACK_DRIVE_MOTOR = new CANTalon(RobotMap.LEFT_BACK_DRIVE_MOTOR_PIN);
        LEFT_TOP_DRIVE_MOTOR = new CANTalon(RobotMap.LEFT_TOP_DRIVE_MOTOR_PIN);

        // right motors
        RIGHT_FRONT_DRIVE_MOTOR = new CANTalon(RobotMap.RIGHT_FRONT_DRIVE_MOTOR_PIN);
        RIGHT_BACK_DRIVE_MOTOR = new CANTalon(RobotMap.RIGHT_BACK_DRIVE_MOTOR_PIN);
        RIGHT_TOP_DRIVE_MOTOR = new CANTalon(RobotMap.RIGHT_TOP_DRIVE_MOTOR_PIN);

        // pid output groups
        LEFT_DRIVE_MOTORS = new PIDOutputGroup(LEFT_FRONT_DRIVE_MOTOR, LEFT_BACK_DRIVE_MOTOR, LEFT_TOP_DRIVE_MOTOR);
        RIGHT_DRIVE_MOTORS = new PIDOutputGroup(RIGHT_FRONT_DRIVE_MOTOR, RIGHT_BACK_DRIVE_MOTOR, RIGHT_TOP_DRIVE_MOTOR);
        ALL_DRIVE_MOTORS = new PIDOutputGroup(LEFT_DRIVE_MOTORS, RIGHT_DRIVE_MOTORS);

        // encoders
        LEFT_DRIVE_ENCODER = new Encoder(RobotMap.LEFT_DRIVE_ENCODER_PIN_A, RobotMap.LEFT_DRIVE_ENCODER_PIN_B);
        RIGHT_DRIVE_ENCODER = new Encoder(RobotMap.RIGHT_DRIVE_ENCODER_PIN_A, RobotMap.RIGHT_DRIVE_ENCODER_PIN_B);
        DRIVE_ENCODERS = new MedianPIDSource(LEFT_DRIVE_ENCODER, RIGHT_DRIVE_ENCODER);
    }

Add methods that access the encoders also, for the sake of good practice.

.. code-block:: java

    /**
     * Gets the value of the left drive encoders.
     */
    public static double getLeftEncoder()
    {
        return LEFT_DRIVE_ENCODER.get();
    }

    /**
     * Gets the value of the right drive encoders.
     */
    public static double getRightEncoder()
    {
        return RIGHT_DRIVE_ENCODER.get();
    }

    /**
     * Gets the value of the drive encoders, averaged.
     */
    public static double getDriveEncoders()
    {
        return DRIVE_ENCODERS.get();
    }

    /**
     * Resets the drive encoders.
     */
    public static void resetEncoders()
    {
        LEFT_DRIVE_ENCODER.reset();
        RIGHT_DRIVE_ENCODER.reset();
    }

Gyro
^^^^

Then, add the gyro. Different Gyros will have different ways to construct them. For example, if it's a NavX gyro, then use the type AHRS:

.. code-block:: java
    :emphasize-lines: 1,5

    public static AHRS DRIVE_GYRO;

    public Drive()
    {
        DRIVE_GYRO = new AHRS(RobotMap.MXP_PORT);
    }

Often, a custom implementation of the gyro is used, so make sure that the gyro is wrapped in the custom implementation also.

Note that how to access the gyro depends on which gyro and which gyro implementation is used. It is wise to add a getRobotAngle() method using the gyro, but the implementation varies from gyro to gyro, so it cannot be shown here.

PID Controllers
^^^^^^^^^^^^^^^

Lastly, add the PID Controllers. As usual, declare them in the class.

.. code-block:: java
	
	// PID Controllers
	public static PIDController DRIVE_DISTANCE_CONTROLLER;
	public static PIDController DRIVE_ANGLE_CONTROLLER;

Now, the DRIVE_ANGLE_CONTROLLER needs a PIDOutput that turns the wheels opposite to each other, since no PIDOutput in this subsystem does this. To do this, `write your own custom PIDOutput <./../../../robotcode/components/pid/pidoutput.html>`_ that outputs opposite values to the left and right motors, to make the robot turn. Call it TurnPIDOutput.

Here's a TurnPIDOutput example:

.. code-block:: java

	public class TurnPIDOutput implements PIDOutput
	{
	    PIDOutput left;
	    PIDOutput right;

	    public TurnPIDOutput(PIDOutput left, PIDOutput right)
	    {
	        this.left = left;
	        this.right = right;
	    }

	    // turning left backwards and right forwards makes the robot turn a positive counterclockwise
	    @Override
	    public void pidWrite(double value)
	    {
	        left.pidWrite(-value);
	        right.pidWrite(value);
	    }
	}

Now, add to the constructor:

.. code-block:: java
    :emphasize-lines: 17,27,28,29

    public Drive()
    {
        // Left Motors
        LEFT_FRONT_DRIVE_MOTOR = new CANTalon(RobotMap.LEFT_FRONT_DRIVE_MOTOR_PIN);
        LEFT_BACK_DRIVE_MOTOR = new CANTalon(RobotMap.LEFT_BACK_DRIVE_MOTOR_PIN);
        LEFT_TOP_DRIVE_MOTOR = new CANTalon(RobotMap.LEFT_TOP_DRIVE_MOTOR_PIN);

        // Right Motors
        RIGHT_FRONT_DRIVE_MOTOR = new CANTalon(RobotMap.RIGHT_FRONT_DRIVE_MOTOR_PIN);
        RIGHT_BACK_DRIVE_MOTOR = new CANTalon(RobotMap.RIGHT_BACK_DRIVE_MOTOR_PIN);
        RIGHT_TOP_DRIVE_MOTOR = new CANTalon(RobotMap.RIGHT_TOP_DRIVE_MOTOR_PIN);

        // PID Output Groups
        LEFT_DRIVE_MOTORS = new PIDOutputGroup(LEFT_FRONT_DRIVE_MOTOR, LEFT_BACK_DRIVE_MOTOR, LEFT_TOP_DRIVE_MOTOR);
        RIGHT_DRIVE_MOTORS = new PIDOutputGroup(RIGHT_FRONT_DRIVE_MOTOR, RIGHT_BACK_DRIVE_MOTOR, RIGHT_TOP_DRIVE_MOTOR);
        ALL_DRIVE_MOTORS = new PIDOutputGroup(LEFT_DRIVE_MOTORS, RIGHT_DRIVE_MOTORS);
        TurnPIDOutput TURN_PID_OUTPUT = new TurnPIDOutput(LEFT_DRIVE_MOTORS, RIGHT_DRIVE_MOTORS);

        // Encoders
        LEFT_DRIVE_ENCODER = new Encoder(RobotMap.LEFT_DRIVE_ENCODER_PIN_A, RobotMap.LEFT_DRIVE_ENCODER_PIN_B);
        RIGHT_DRIVE_ENCODER = new Encoder(RobotMap.RIGHT_DRIVE_ENCODER_PIN_A, RobotMap.RIGHT_DRIVE_ENCODER_PIN_B);
        DRIVE_ENCODERS = new MedianPIDSource(LEFT_DRIVE_ENCODER, RIGHT_DRIVE_ENCODER);

        // Gyro
        DRIVE_GYRO = new AHRS(RobotMap.MXP_PORT);

        // PID Controllers
        DRIVE_DISTANCE_CONTROLLER = new PIDController(0.015, 0.001, 0.0, DRIVE_ENCODERS, ALL_DRIVE_MOTORS);
        DRIVE_ANGLE_CONTROLLER = new PIDController(0.1, 0.0, 0.0, DRIVE_GYRO, TURN_PID_OUTPUT);
    }

And that's it! Now, the Drive subsystem is complete.

On creating a command that uses this subsystem, see `Tank Drive <./../../commands/examples/commands/tankdrive.html>`_.

Final code
^^^^^^^^^^

.. code-block:: java

    /**
     * Drive subsystem
     */
    public class Drive extends Subsystem
    {
        // Left Motors
        private static CANTalon LEFT_FRONT_DRIVE_MOTOR;
        private static CANTalon LEFT_BACK_DRIVE_MOTOR;
        private static CANTalon LEFT_TOP_DRIVE_MOTOR;

        // Right Motors
        private static CANTalon RIGHT_FRONT_DRIVE_MOTOR;
        private static CANTalon RIGHT_BACK_DRIVE_MOTOR;
        private static CANTalon RIGHT_TOP_DRIVE_MOTOR;

        // PID Output Groups
        public static PIDOutputGroup LEFT_DRIVE_MOTORS;
        public static PIDOutputGroup RIGHT_DRIVE_MOTORS;
        public static PIDOutputGroup ALL_DRIVE_MOTORS;

        // Encoders
        public static Encoder LEFT_DRIVE_ENCODER;
        public static Encoder RIGHT_DRIVE_ENCODER;
        public static MedianPIDSource DRIVE_ENCODERS;

        // Gyro
        public static AHRS DRIVE_GYRO;

        // PID Controllers
        public static PIDController DRIVE_DISTANCE_CONTROLLER;
        public static PIDController DRIVE_ANGLE_CONTROLLER;

        public Drive()
        {
            // Left Motors
            LEFT_FRONT_DRIVE_MOTOR = new CANTalon(RobotMap.LEFT_FRONT_DRIVE_MOTOR_PIN);
            LEFT_BACK_DRIVE_MOTOR = new CANTalon(RobotMap.LEFT_BACK_DRIVE_MOTOR_PIN);
            LEFT_TOP_DRIVE_MOTOR = new CANTalon(RobotMap.LEFT_TOP_DRIVE_MOTOR_PIN);

            // Right Motors
            RIGHT_FRONT_DRIVE_MOTOR = new CANTalon(RobotMap.RIGHT_FRONT_DRIVE_MOTOR_PIN);
            RIGHT_BACK_DRIVE_MOTOR = new CANTalon(RobotMap.RIGHT_BACK_DRIVE_MOTOR_PIN);
            RIGHT_TOP_DRIVE_MOTOR = new CANTalon(RobotMap.RIGHT_TOP_DRIVE_MOTOR_PIN);

            // PID Output Groups
            LEFT_DRIVE_MOTORS = new PIDOutputGroup(LEFT_FRONT_DRIVE_MOTOR, LEFT_BACK_DRIVE_MOTOR, LEFT_TOP_DRIVE_MOTOR);
            RIGHT_DRIVE_MOTORS = new PIDOutputGroup(RIGHT_FRONT_DRIVE_MOTOR, RIGHT_BACK_DRIVE_MOTOR, RIGHT_TOP_DRIVE_MOTOR);
            ALL_DRIVE_MOTORS = new PIDOutputGroup(LEFT_DRIVE_MOTORS, RIGHT_DRIVE_MOTORS);
            TurnPIDOutput TURN_PID_OUTPUT = new TurnPIDOutput(LEFT_DRIVE_MOTORS, RIGHT_DRIVE_MOTORS);

            // Encoders
            LEFT_DRIVE_ENCODER = new Encoder(RobotMap.LEFT_DRIVE_ENCODER_PIN_A, RobotMap.LEFT_DRIVE_ENCODER_PIN_B);
            RIGHT_DRIVE_ENCODER = new Encoder(RobotMap.RIGHT_DRIVE_ENCODER_PIN_A, RobotMap.RIGHT_DRIVE_ENCODER_PIN_B);
            DRIVE_ENCODERS = new MedianPIDSource(LEFT_DRIVE_ENCODER, RIGHT_DRIVE_ENCODER);

            // Gyro
            DRIVE_GYRO = new AHRS(RobotMap.MXP_PORT);

            // PID Controllers
            DRIVE_DISTANCE_CONTROLLER = new PIDController(0.015, 0.001, 0.0, DRIVE_ENCODERS, ALL_DRIVE_MOTORS);
            DRIVE_ANGLE_CONTROLLER = new PIDController(0.1, 0.0, 0.0, DRIVE_GYRO, TURN_PID_OUTPUT);
        }

        public void initDefaultCommand()
        {

        }
         
        /**
         * Sets the left drive motors.
         */
        public static void setLeftMotors(double value)
        {
            LEFT_DRIVE_MOTORS.set(value);
        }

        /**
         * Sets the right drive motors.
         */
        public static void setRightMotors(double value)
        {
            RIGHT_DRIVE_MOTORS.set(value);
        }

        /**
         * Sets all of the drive motors.
         */
        public static void setAllMotors(double value)
        {
            ALL_DRIVE_MOTORS.set(value);
        }

        /**
         * Gets the value of the left drive encoders.
         */
        public static double getLeftEncoder()
        {
            return LEFT_DRIVE_ENCODER.get();
        }

        /**
         * Gets the value of the right drive encoders.
         */
        public static double getRightEncoder()
        {
            return RIGHT_DRIVE_ENCODER.get();
        }

        /**
         * Gets the value of the drive encoders, averaged.
         */
        public static double getDriveEncoders()
        {
            return DRIVE_ENCODERS.get();
        }

        /**
         * Resets the drive encoders.
         */
        public static void resetEncoders()
        {
            LEFT_DRIVE_ENCODER.reset();
            RIGHT_DRIVE_ENCODER.reset();
        }
    }

.. toctree::
	:glob:
	:maxdepth: 10
	:caption: Examples
	:hidden:

	