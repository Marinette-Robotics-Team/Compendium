Autonomous Testing
==================

Before testing autonomous, you need to make sure that your encoders are fully functional, and also make sure that the drive PIDs are completely tuned.

Testing the Encoders
^^^^^^^^^^^^^^^^^^^^

Add a command that prints out all of the encoder values. It can look something like this:

.. code-block:: java

	public class TestEncoders extends Command
	{
	    // Called repeatedly when this Command is scheduled to run
	    @Override
	    protected void execute()
	    {
	        SmartDashboard.putNumber("LEFT FRONT ENCODER", Drive.LEFT_FRONT_ENCODER.get());
	        SmartDashboard.putNumber("LEFT BACK ENCODER", Drive.LEFT_BACK_ENCODER.get());
	        SmartDashboard.putNumber("RIGHT FRONT ENCODER", Drive.RIGHT_FRONT_ENCODER.get());
	        SmartDashboard.putNumber("RIGHT BACK ENCODER", Drive.RIGHT_BACK_ENCODER.get());
	    }
	    
	    // Make this return true when this Command no longer needs to run execute()
	    @Override
	    protected boolean isFinished()
	    {
	        return false;
	    }
	}

Then, in the Robot file's robotInit(), add:

.. code-block:: java

	// construct the command
	Command testEncoders = new TestEncoders();
	// make it run when disabled, since you can read from encoders while disabled
	testEncoders.setRunWhenDisabled(true);
	// start the command
	testEncoders.start();

Now, you can observe the encoder values in SmartDashboard.

Now, turn on the robot and watch the robot as other people push it straight forward at a steady rate. Make sure all of the encoders increase at the same time, and at the end, are about the same value. If an encoder stays at 0, then either it's not correctly connected, the pins are wrong, or it has no power. If an encoder increases slowly, or fails to increase at some times, it's possible that the encoder shaft is slipping, or the disk is damaged, or a multitude of reasons. A scope can help diagnose issues with encoders not working, but that's more of an electrical task than a software one.

If any encoders' signal wires are flipped, their number will be negative when the robot goes forwards. Either flip the signal wires coming from the encoder, or flip the pins in RobotMap.

Once all the issues with encoders are resolved, we can move on to the PIDs.

Testing the PIDs
^^^^^^^^^^^^^^^^

Before testing autonomous code, if the drive PIDs have not yet been tuned, you will have to `tune the drive PIDs <./../../robotcode/components/pid/pidcontroller2.html>`_.

To test these drive PIDs, on a straight line, mark 2 pieces of tape, 10 feet away from each other. When the encoder values are 0, start the robot at one piece of tape, and push it forwards towards the other piece of tape until it is the same position relative to the tape as it started. This should be exactly 10 feet. Then, the final encoder values are the number of encoder ticks for the robot to move 10 feet.

Now, use a DriveForward command to start the robot at one end, and drive forward towards the other piece of tape, using the same number of ticks as obtained by pushing the robot. The robot should be in the exact same position relative to the tape after the command ends. If it isn't very accurate, then the PIDs probably need to be tuned better. Also, if time allows, try to repeat this with other distances, such as 5 feet. Then, you can be sure that the PIDs are truly tuned.

Note that this is one of the most common issues in autonomous. Often, it may be incredibly difficult to get autonomous to work, but once the PIDs are tuned, eveything magically falls into place. So, be absolutely sure that the PIDs are tuned well (not just *good enough*) before moving on to autonomous testing.

Once all the PIDs are known to be tuned, we can move on to actually testing autonomous.

Testing the autonomous commands
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Firstly, use tape to mark all of the important field markers. Remember that not only is each field different, but also that drivers are not allowed to use measuring tools when placing the robot. Because of this, the drivers should practice placing the robot in the same spot each time relative to the tapes that you put on the carpet. Communicate this information to drive coach as well.

Then, put the robot on the practice field, and run the autonomous command to make sure it does what it's supposed to do. If the robot is consistently missing a target by the same amount each time, then it's OK to manually adjust the distances you're passing into the autonomous commands. For example, if the robot is consistently driving too far each time, it may be OK to subtract off a few ticks in code to decrease the distance. However, in most cases, this is caused by a poorly tuned PID or incorrect calculations, so make sure these aren't the issue first.

Autonomous testing takes a long time, since you will have to repeatedly run the robot, adjust numbers, deploy and move it back. Because of this, make sure that you either have access to a twin robot (this usually happens), or that you have time at the end of build season to test autonomous (this never happens).

.. toctree::
	:glob:
	:maxdepth: 10
	:caption: Contents

