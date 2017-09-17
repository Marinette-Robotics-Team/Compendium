PID Components
==============

These are not hardware components on the robot. However, they are still components that belong in their respective subsystems. For example, a Drive PID Controller should be in the Drive subsystem.

Note that most numbered-value sensors, like Encoders, are already PIDSources, and thus do not require any additional modification.

The guide for PIDSources and PIDOutputs is intended for those who understand how to use interfaces, and must implement their own PIDSources or PIDOutputs.

.. toctree::
	:glob:
	:maxdepth: 10
	:caption: Contents:
	
	pidcontroller0
	pidcontroller1
	pidcontroller2
	pidsource
	pidoutput