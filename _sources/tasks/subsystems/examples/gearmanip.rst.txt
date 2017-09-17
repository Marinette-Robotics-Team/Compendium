Gear manipulator subsystem
==========================

In this example, we create a subsystem designed to control the Gear Manipulator subsystem from 2017.

In this subsystem, a belt motor moves a carriage between two endpoints, the front and the back of the robot. At each end, there is a reed switch that tells when the carriage has reached that end. There is also an encoder that tracks the position of the belt.

Attached to the carriage, there is also a claw that is made up of two grabbers that open and close, controlled by double solenoids. One grabber is in the front, and one grabber is in the back. They open and close to allow gears in. When the solenoid is in forward position, the claw is closed, and if it is in reverse position, the claw is open.

Start out by `creating the subsystem file <./../subsystems.html#create-the-subsystem-s-file>`_. You'll start out with this code:

.. code-block:: java

    /**
     *
     */
    public class GearManipulator extends Subsystem {

        // Put methods for controlling this subsystem
        // here. Call these from Commands.

        public GearManipulator()
        {

        }

        public void initDefaultCommand() {
            // Set the default command for a subsystem here.
            //setDefaultCommand(new MySpecialCommand());
        }
    }

Motor
^^^^^

First, create the Victor that represents the belt motor.

Declare it in the class:

.. code-block:: java

	private static Victor BELT_MOTOR;

and initialize it in the constructor:

.. code-block:: java

    public GearManipulator()
    {
        BELT_MOTOR = new Victor(RobotMap.BELT_MOTOR_PIN);
    }

Then, add the method setBeltMotor, so that commands can aset the motor power. It is generally good practice to expose only public setters, and make the field itself private.

.. code-block:: java
    :emphasize-lines: 3

	public static void setBeltMotor(double value)
	{
	    BELT_MOTOR.set(value);
	}

Solenoids
^^^^^^^^^

Similarly, the front and back claw DoubleSolenoids must be created also.

Declare them in the class:

.. code-block:: java

    private static DoubleSolenoid FRONT_CLAW;
    private static DoubleSolenoid BACK_CLAW;

and initialize them in the constructor:

.. code-block:: java
    :emphasize-lines: 5,6

    public GearManipulator()
    {
        BELT_MOTOR = new Victor(RobotMap.BELT_MOTOR_PIN);

        FRONT_CLAW = new DoubleSolenoid(RobotMap.FRONT_CLAW_PIN_A, RobotMap.FRONT_CLAW_PIN_B);
        BACK_CLAW = new DoubleSolenoid(RobotMap.BACK_CLAW_PIN_A, RobotMap.BACK_CLAW_PIN_B);
    }

Also, create the setters for these claws.

.. code-block:: java

    public static void openFrontClaw()
    {
        FRONT_CLAW.set(DoubleSolenoid.Value.kReverse);
    }

    public static void closeFrontClaw()
    {
        FRONT_CLAW.set(DoubleSolenoid.Value.kForward);
    }

    public static void openBackClaw()
    {
        BACK_CLAW.set(DoubleSolenoid.Value.kReverse);
    }

    public static void closeBackClaw()
    {
        BACK_CLAW.set(DoubleSolenoid.Value.kForward);
    }

Reed Switches
^^^^^^^^^^^^^

Next up is to add the reed switch sensors to the subsystem. Since reed switches are just digital inputs, we treat them as such. Declare the DigitalInputs in the class:

.. code-block:: java

    private static DigitalInput FRONT_REED_SWITCH;
    private static DigitalInput BACK_REED_SWITCH;

and initialize them in the constructor:

.. code-block:: java
    :emphasize-lines: 8,9

    public GearManipulator()
    {
        BELT_MOTOR = new Victor(RobotMap.BELT_MOTOR_PIN);

        FRONT_CLAW = new DoubleSolenoid(RobotMap.FRONT_CLAW_PIN_A, RobotMap.FRONT_CLAW_PIN_B);
        BACK_CLAW = new DoubleSolenoid(RobotMap.BACK_CLAW_PIN_A, RobotMap.BACK_CLAW_PIN_B);
        
        FRONT_REED_SWITCH = new DigitalInput(RobotMap.FRONT_REED_SWITCH_PIN);
        BACK_REED_SWITCH = new DigitalInput(RobotMap.BACK_REED_SWITCH_PIN);
    }

Now, add the getters that tell commands about the state of the reed switches. Remember that reed switches are often active low, so that means the DigitalInput.get() gives false when the reed switch is close to a magnet. So, return the opposite of the DigitalInput reading for a sensible result.

.. code-block:: java

    public static boolean getFrontReedSwitchActive()
    {
        return !(FRONT_REED_SWITCH.get());
    }

    public static boolean getBackReedSwitchActive()
    {
        return !(BACK_REED_SWITCH.get());
    }

Encoder
^^^^^^^

Now, add the encoder to the subsystem. Declare it in the class:

.. code-block:: java

    private static Encoder BELT_ENCODER;

and initialize it in the constructor:

.. code-block:: java
    :emphasize-lines: 11

    public GearManipulator()
    {
        BELT_MOTOR = new Victor(RobotMap.BELT_MOTOR_PIN);

        FRONT_CLAW = new DoubleSolenoid(RobotMap.FRONT_CLAW_PIN_A, RobotMap.FRONT_CLAW_PIN_B);
        BACK_CLAW = new DoubleSolenoid(RobotMap.BACK_CLAW_PIN_A, RobotMap.BACK_CLAW_PIN_B);
        
        FRONT_REED_SWITCH = new DigitalInput(RobotMap.FRONT_REED_SWITCH_PIN);
        BACK_REED_SWITCH = new DigitalInput(RobotMap.BACK_REED_SWITCH_PIN);

        BELT_ENCODER = new Encoder(RobotMap.BELT_ENCODER_PIN_A, RobotMap.BELT_ENCODER_PIN_B);
    }

Now, add a getter for the encoder.

.. code-block:: java

    public static double getBeltEncoder()
    {
        return BELT_ENCODER.get();
    }

PID Controller
^^^^^^^^^^^^^^

Lastly, add the PID Controller linking the belt encoder to the belt motor. Add the PID Controller to the subsystem:

.. code-block:: java

    public static PIDController BELT_POSITION_CONTROLLER;

and initialize it in the constructor:

.. code-block:: java
    :emphasize-lines: 11

    public GearManipulator()
    {
        BELT_MOTOR = new Victor(RobotMap.BELT_MOTOR_PIN);

        FRONT_CLAW = new DoubleSolenoid(RobotMap.FRONT_CLAW_PIN_A, RobotMap.FRONT_CLAW_PIN_B);
        BACK_CLAW = new DoubleSolenoid(RobotMap.BACK_CLAW_PIN_A, RobotMap.BACK_CLAW_PIN_B);
        
        FRONT_REED_SWITCH = new DigitalInput(RobotMap.FRONT_REED_SWITCH_PIN);
        BACK_REED_SWITCH = new DigitalInput(RobotMap.BACK_REED_SWITCH_PIN);

        BELT_ENCODER = new Encoder(RobotMap.BELT_ENCODER_PIN_A, RobotMap.BELT_ENCODER_PIN_B);

        BELT_POSITION_CONTROLLER = new PIDController(0.015, 0.001, 0.0, BELT_ENCODER, BELT_MOTOR);
    }


And that's it! Now, the GearManipulator subsystem has all the necessary methods for writing commands.

Final code
^^^^^^^^^^

.. code-block:: java

    /**
     * GearManipulator subsystem
     */
    public class GearManipulator extends Subsystem
    {
        
        private static Victor BELT_MOTOR;

        private static DoubleSolenoid FRONT_CLAW;
        private static DoubleSolenoid BACK_CLAW;

        private static DigitalInput FRONT_REED_SWITCH;
        private static DigitalInput BACK_REED_SWITCH;

        private static Encoder BELT_ENCODER;

        public static PIDController BELT_POSITION_CONTROLLER;

        public GearManipulator()
        {
            BELT_MOTOR = new Victor(RobotMap.BELT_MOTOR_PIN);

            FRONT_CLAW = new DoubleSolenoid(RobotMap.FRONT_CLAW_PIN_A, RobotMap.FRONT_CLAW_PIN_B);
            BACK_CLAW = new DoubleSolenoid(RobotMap.BACK_CLAW_PIN_A, RobotMap.BACK_CLAW_PIN_B);
            
            FRONT_REED_SWITCH = new DigitalInput(RobotMap.FRONT_REED_SWITCH_PIN);
            BACK_REED_SWITCH = new DigitalInput(RobotMap.BACK_REED_SWITCH_PIN);

            BELT_ENCODER = new Encoder(RobotMap.BELT_ENCODER_PIN_A, RobotMap.BELT_ENCODER_PIN_B);

            BELT_POSITION_CONTROLLER = new PIDController(0.015, 0.001, 0.0, BELT_ENCODER, BELT_MOTOR);
        }

        public void initDefaultCommand()
        {

        }

        public static void setBeltMotor(double value)
        {
            BELT_MOTOR.set(value);
        }

        public static void openFrontClaw()
        {
            FRONT_CLAW.set(DoubleSolenoid.Value.kReverse);
        }

        public static void closeFrontClaw()
        {
            FRONT_CLAW.set(DoubleSolenoid.Value.kForward);
        }

        public static void openBackClaw()
        {
            BACK_CLAW.set(DoubleSolenoid.Value.kReverse);
        }

        public static void closeBackClaw()
        {
            BACK_CLAW.set(DoubleSolenoid.Value.kForward);
        }

        public static boolean getFrontReedSwitchActive()
        {
            return !(FRONT_REED_SWITCH.get());
        }

        public static boolean getBackReedSwitchActive()
        {
            return !(BACK_REED_SWITCH.get());
        }

        public static double getBeltEncoder()
        {
            return BELT_ENCODER.get();
        }

    }

.. toctree::
	:glob:
	:maxdepth: 10
	:caption: Examples
	
	