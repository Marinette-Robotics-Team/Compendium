Digital Inputs
==============

Components on the robot that only take one DIO input pin are usually DigitalInputs.

DigitalInput.get() returns true when the signal line is high.
DigitalInput.get() returns false when the signal line is low.

Creating a digital input:

.. code-block:: java

	DigitalInput limitSwitch = new DigitalInput(9);

Accessing a digital input:

.. code-block:: java

	limitSwitch.get()

.. toctree::
	:glob:
	:maxdepth: 10
	:caption: Contents:
	
	limitswitches
	reedswitches