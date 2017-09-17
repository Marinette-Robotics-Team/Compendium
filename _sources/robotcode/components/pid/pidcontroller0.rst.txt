Why use a PID Controller?
=========================

This section focuses on the reasons for using a PID Controller, and their advantage over other systems that we may write.

A PID Controller is a way to use a sensor to control a motor, without the motor jerking around and going crazy. Instead, a PID Controller has 3 adjustable values that can be tuned to make the motor behave nicely.

Imagine that we want a motor to turn 1000 encoder ticks. One simple way might be:

.. code-block:: java

    @Override
    protected void execute()
    {
        // if we need to go forward
        if (encoder.get() < 1000)
        {
            // go forward
            motor.set(1);
        }
		
        // if we need to go backward
        if (encoder.get() > 1000)
        {
            // go backward
            motor.set(-1);
        }
    }

But, this code has problems. Note how if encoder is even one tick off, the motor will still turn at full power. For example, if it's at 999 ticks, it will still continue to turn at full power forward, causing it to overshoot by far, due to the momentum of the motor's spinning. It will then try to go back, and then start oscillating back and forth, trying to reach 1000 ticks.

That means we want to slow down the motor as we get closer and closer to the goal. One way to do this is to directly relate how far we are from the goal to how much power we give to the motor. That way, the closer we get the goal, the less power we give to the motor, slowing it down.

To do this, we need to know how far we are from the goal. We call this error:

.. code-block:: java

    // how far we are from the goal
    double error = 1000 - encoder.get();

Since this is a direct relation, we also have to choose a constant multiplier. This can't just be 1 since otherwise when the encoder is at 999, then error is equal to 1000 - 999 = 1. Then, if the multiplier is just 1, then 1 * error = 1 * 1 = 1, which gives us exactly the same thing as the dumb way of doing this above. Thus, we choose a smaller value, like 0.001.

.. code-block:: java

    // the constant multiplier linking error to output
    double P = 0.001;

Now, the direct relation is clear. Simply make the output equal to the error times the constant multiplier.

.. code-block:: java

    motor.set(error * P);

Now, implement that into the command:

.. code-block:: java

    @Override
    protected void execute()
    {
        // how far we are from the goal
        double error = 1000 - encoder.get();

        // the constant multiplier linking error to output
        double P = 0.001;
        
        motor.set(error * P);
    }

Now, all you have to do is to tune the P value until the motor goes accurately to 1000 ticks.

However, there is another issue in many systems, and that is static friction. What often happens with only a direct relation from error to the motor is that static friction can get in the way of accuracy.

So, if we're a bit off of the target, and if we're stopped from static friction, then we need to ramp up our motor output over time to correct it.

This is accomplished using something called an Integral value. Calculus people will know that an integral is the area under a curve, but the important part to know is that the integral builds up as we are off of the target for longer periods of time (e.g. stopped due to static friction).

Then, when the integral builds up to a large enough value, it overcomes static friction by a little bit, and moves the robot to its intended target. So, we need to add the integral to our controller:

.. code-block:: java
    
    // calculate integral (if you don't know calculus, don't bother)
    double integral = integral + (error * deltaTime);

    // the constant multiplier linking the integral of error to output
    double I = 0.0005;

Then, instead of

.. code-block:: java

	motor.set(error * P);

use:

.. code-block:: java

	motor.set((error * P) + (integral * I));

Now, our controller looks like:

.. code-block:: java

    @Override
    protected void execute()
    {
        // how far we are from the goal
        double error = 1000 - encoder.get();

        // the constant multiplier linking error to output
        double P = 0.001;
        
        // calculate integral (if you don't know calculus, don't bother)
        double integral = integral + (error * deltaTime);

        // the constant multiplier linking the integral of error to output
        double I = 0.0005;

        motor.set((error * P) + (integral * I));
    }

Now, however, we have the issue that the I value builds really quickly at the start as the error is high in the beginning. Then, when we actually reach the goal, we are outputting too much power to the motor, and now we overshoot.

To fix this, we need something that limits out motor power if we're approaching the target very quickly, so that we don't overshoot it, but also doesn't interfere with the rest of our system if we're only making small, slow adjustments.

We can do this by examining how fast we're approaching the target. This is called the Derivative of the error. That way, if our error is decreasing quickly, we know to slow down the motor. But, if our error isn't decreasing quickly, and it's just a small adjustment, the Derivative is small, so it doesn't interfere.

.. code-block:: java
    
    // calculate derivative (if you don't know calculus, don't bother)
    double derivative = (error - old) / deltaTime;
    old = error;

    // the constant multiplier linking the derivative of error to output
    double D = 0.01;

Now, our controller looks like this:

.. code-block:: java

    @Override
    protected void execute()
    {
        // how far we are from the goal
        double error = 1000 - encoder.get();

        // the constant multiplier linking error to output
        double P = 0.001;
        
        // calculate integral (if you don't know calculus, don't bother)
        double integral = integral + (error * deltaTime);

        // the constant multiplier linking the integral of error to output
        double I = 0.0005;

        // calculate derivative (if you don't know calculus, don't bother)
        double derivative = (error - old) / deltaTime;
        old = error;

        // the constant multiplier linking the derivative of error to output
        double D = 0.01;

        motor.set((error * P) + (integral * I) + (derivative * D));
    }

Now, this is exactly what a PID Controller is. It's like a self-contained command, that takes the P, I, and D values, calculates the error, integral, and derivative, and outputs to the motor. So, instead of making this command by hand, we simply need to create a PIDController and enable it.

In practice, though, usually just a P value works here, with I and D left as 0. If not, then a P and an I value will usually do the trick, with D left as 0. We rarely ever use P, I, and D all together, since usually, a PID can be tuned enough even without all three, and a D value would then just waste time and be unnecessarily complex.

Although well tuned PID Controllers can work very well, note that a poorly tuned PID Controller still can jerk around and go crazy, which is something called "Possessed Robot Syndrome".

On how to use a PID Controller, see `Using PID Controllers <pidcontroller1.html>`_.

On how to tune a PID Controller, see `Tuning PID Controllers <pidcontroller2.html>`_.

.. toctree::
	:glob:
	:maxdepth: 10
	:caption: Contents:
	
	