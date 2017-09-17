Climber subsystem
=================

In this example, we will create a very basic subsystem that only consists of one speed controller that turns a motor.

The only component will be that VictorSP called CLIMBER_MOTOR, and the only public method will be setClimberMotor, which is obviously used to set the power of CLIMBER_MOTOR.

Start out by `creating the subsystem file <./../subsystems.html#create-the-subsystem-s-file>`_. You'll start out with this code:

.. code-block:: java

    /**
     *
     */
    public class Climber extends Subsystem {

        // Put methods for controlling this subsystem
        // here. Call these from Commands.

        public Climber()
        {

        }

        public void initDefaultCommand() {
            // Set the default command for a subsystem here.
            //setDefaultCommand(new MySpecialCommand());
        }
    }

Now, create the Victor that represents the climber motor.

Declare it in the class:

.. code-block:: java

	private static Victor CLIMBER_MOTOR;

and initialize it in the constructor:

.. code-block:: java

    public Climber()
    {
        CLIMBER_MOTOR = new Victor(RobotMap.CLIMBER_MOTOR_PIN);
    }

If RobotMap.CLIMBER_MOTOR_PIN does not yet exist, make sure to add it to RobotMap also.

Then, add the method setClimberMotor.

.. code-block:: java

	public static void setClimberMotor(double value)
	{
	    CLIMBER_MOTOR.set(value);
	}

And that's it! Now, the Climber subsystem has a method that sets the climber motor, using its speed controller component.

On creating a command that uses this subsystem, see `ClimberContinuous <./../../commands/examples/commands/climber.html>`_.

Final code
^^^^^^^^^^

.. code-block:: java

    /**
     * Climber subsystem
     */
    public class Climber extends Subsystem
    {

        private static Victor CLIMBER_MOTOR;

        public Climber()
        {
            CLIMBER_MOTOR = new Victor(RobotMap.CLIMBER_MOTOR_PIN);
        }

        public void initDefaultCommand()
        {

        }

        public static void setClimberMotor(double value)
        {
            CLIMBER_MOTOR.set(value);
        }
    }

.. toctree::
	:glob:
	:maxdepth: 10
	:caption: Examples
	:hidden:

