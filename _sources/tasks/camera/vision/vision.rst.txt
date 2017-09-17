Vision
======

This section discusses how to use Machine Vision.

Before reading this guide, it's highly recommended to watch the `Cheesy Poofs' machine vision video <https://www.youtube.com/watch?v=rLwOkAJqImo>`_.

**Note:** Working on machine vision takes a lot of time and programming skill. If you choose to work on machine vision, note that you may have to dedicate your entire season to it.

Often, machine vision is not necessary for success. Preprogrammed commands built on dead reckoning using encoders and a gyro is definitely capable of completing most tasks in autonomous mode. However, for some very complex tasks, such as autonomously aiming a shooter at a goal from any position while on the move, a preprogrammed command may not be enough. Our team probably will not attempt many of these more complex tasks, so machine vision is seldom necessary.

However, if in the future we become a higher tier team, machine vision may become the standard. So, for future-proofing's sake, I provide you my guide on using machine vision.

Choosing a platform
-------------------

Before you start working on machine vision code, you need to choose a platform for it to run on. There are three main ways to run it:

 - The RoboRIO
    It's possible to run machine vision code directly on the RoboRIO, using GRIP or WPILib's OpenCV library. However, this is slow and risky. If the camera code takes too much of the RoboRIO's processing power, it may not be able to run the rest of the robot's commands, making the robot unusable. I would not recommend this option.

 - Raspberry Pi (also called RPi)
    This small, cheap single board computer can use OpenCV to do machine vision. It's a separate processor from the RoboRIO, which eliminates the risk of messing up the rest of the robot if the machine vision code runs too slowly. However, setting up a raspberry pi with OpenCV is fairly difficult. It requires some familiarity with Linux commands. Unfortunately, raspberry pis are usually not compatible with most USB webcams, which means it must use a special Raspberry Pi Camera. Thankfully, RPi and the RPiCam combined only cost about $65.

 - Kangaroo
    The Kangaroo is by far the most expensive option here, costing an extra $150-$200. It is a powerful, onboard computer that runs Windows, meaning that it is not difficult to set up. There's been some whispering that our team might buy one of these if we need to do machine vision, but it might not happen.

After choosing a platform, you must choose between the two main ways of writing camera code:

 - `GRIP <https://wpilib.screenstepslive.com/s/4485/m/24194/l/463566-introduction-to-grip>`_
    GRIP is WPILib's graphical tool for developing machine vision code. You add components to the GRIP Pipeline, such as filters, and then it generates code for you. Unfortunately, GRIP does not run on raspberry pi's `without some extra effort <https://github.com/WPIRoboticsProjects/GRIP/wiki/Running-GRIP-on-a-Raspberry-Pi-2>`_ due to hardware differences with the RoboRIO, so I would not recommend running GRIP on a RPi.

 - `OpenCV <http://opencv.org/>`_
    OpenCV is an open source machine vision library. I would recommend using the Python programming language, so if you don't know Python and don't want to learn it, I would stick with GRIP. If you already know Python, however, using OpenCV offers a lot of flexibility and customization that GRIP does not offer.

Since `WPILib's documentation already covers using GRIP <https://wpilib.screenstepslive.com/s/4485/m/24194/c/194953>`_, I will cover how to install OpenCV on a Raspberry Pi.

On how to actually use OpenCV, here's a link to an amazing guide called `FRC Programming Done Right <http://frc-pdr.readthedocs.io/en/latest/index.html>`_. I currently don't have the resources nor the time to actually write a guide on it myself, since college is starting.

.. toctree::
	:glob:
	:maxdepth: 10
	:caption: Contents

	rpinstall

