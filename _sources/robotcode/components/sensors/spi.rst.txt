SPI
===

If you use these, I’m sorry. Please try not to use SPI for anything but the FRC Gyro.

You’ll just have to look at the documentation a lot, then figure out how someone else did it in another language to understand what’s going on, then try to shoehorn that into FIRST’s SPI implementation, somehow scrounge out the data you need, then debug it for several weeks.

Here’s an example:

`SPIEncoderPIDSource, used in 2017 <http://nacsvn.aasd.k12.wi.us/repos/nac-software-2017/robot/trunk/Team93Robot2017/src/org/usfirst/frc/team93/robot/utilities/SPIEncoderPIDSource.java>`_

Oh, and ChipSelect1-3 doesn’t work as of 2017.
Use DigitalOutputs instead if you have to. Good luck.



If you're just looking for how to use the FRC Gyro, take a look at the `Steamworks implementation. <https://github.com/Marinette-Robotics-Team/Robot-Source-2016-2017/blob/master/RobotCode2017/src/org/usfirst/frc/team6624/robot/libs/ADXRS453Gyro.java>`_ (This is based on `this implementation<https://gist.github.com/jcorcoran/6d721cf6570f772b51f8>`_, with some added interfaces, like PIDSource.)


.. toctree::
	:glob:
	:maxdepth: 10
	:caption: Contents:
	
	