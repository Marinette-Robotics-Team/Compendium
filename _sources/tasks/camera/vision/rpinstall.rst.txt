Installing OpenCV on the Raspberry Pi
=====================================

This article covers how to install OpenCV on raspberry pi. You might already have a raspberry pi with OpenCV installed. You can check using:

.. code-block:: bash

    $ python
    >>> import cv2
    >>> print cv2.__version__

You should get '3.2.0'.

To install things on the raspberry pi, you will need unrestricted internet access. You can either `try to use a proxy <https://askubuntu.com/questions/347886/apt-get-not-working-behind-restricted-internet-access>`_ (this may not work) or take the raspberry pi home and directly plug it into your router with an ethernet cable.

You will also want an HDMI cable and a monitor so that you can see the rasbperry pi gui, as well as an extra keyboard and mouse.

To power the raspberry pi, you'll need a mini USB power cable (the type of charging cable usually used for most android phones).

Installing Raspbian
-------------------

If you don't already have a microSD card with Raspbian on it, you will have to create one. The minimum SD card size to install Rasbian with NOOBS is 8 GB, but if you don't want to have to deal with mounting a flash drive to download OpenCV, you will want a 16 GB microSD card.

If the Raspberry Pi boots up and shows the desktop, then Raspbian is already installed and you can skip this section.

`Instructions for installing Raspbian with NOOBS can be found here <https://www.raspberrypi.org/documentation/installation/noobs.md>`_, but they are also repeated in this guide.

1. Obtain a microSD card that's at least 8 GB (16 GB recommended for easier installation of OpenCV).

2. Format the SD card to **FAT** using the `SD Card Formatter Tool <https://www.sdcard.org/downloads/formatter_4/>`_. Make ure that **FORMAT SIZE ADJUSTMENT** option is set to **ON**.

3. `Download the NOOBS zip file <https://www.raspberrypi.org/downloads/>`_.

4. Extract it to the SD card. Make sure that the files are at the root directory of the SD card.

5. Put the SD card in the Raspberry Pi and boot it up. To turn on the raspberry pi, you only need to plug it in.

6. Once the raspberry pi has booted up and you can see the NOOBS menu, follow the instructions to install Raspbian with PIXEL. You don't need an internet connection to install.

That should be it for installing Raspbian. 

Installing OpenCV
-----------------

Since Python already comes installed with the Raspbian with NOOBS install above, we don't need to install Python. However, we do need to install OpenCV.

To do so, follow the instructions `here <https://github.com/Tes3awy/OpenCV-3.2.0-Compiling-on-Raspberry-Pi>`_.

These instructions are copied down (since GitHub is sometimes blocked on school wifi), with a few explanations and edits.

1. Update the RPi
^^^^^^^^^^^^^^^^^

Update software:

.. code-block:: bash

	$ sudo apt-get update
	$ sudo apt-get upgrade

Update firmware:

.. code-block:: bash
	
	$ sudo rpi-update

Reboot to apply updates.

.. code-block:: bash

	$ sudo reboot now

2. Install necessary software
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Install cmake, which is used to build OpenCV from its source code

.. code-block:: bash

	$ sudo apt-get install build-essential cmake pkg-config

This next step might take a long while, since it's a lot of software to install.

.. code-block:: bash

	$ sudo apt-get install libjpeg-dev libtiff5-dev libjasper-dev libpng12-dev

	$ sudo apt-get install libgtk2.0-dev libgstreamer0.10-0-dbg libgstreamer0.10-0 libgstreamer0.10-dev libv4l-0 libv4l-dev

	$ sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev libv4l-dev
	$ sudo apt-get install libxvidcore-dev libx264-dev

	$ sudo apt-get install libatlas-base-dev gfortran

	$ sudo apt-get install python-numpy python-scipy python-matplotlib

	$ sudo apt-get install default-jdk ant

	$ sudo apt-get install libgtkglext1-dev
	$ sudo apt-get install v4l-utils

3. Install numpy, which is necessary to use OpenCV
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Install pip, python's package manager:

.. code-block:: bash

	$ wget https://bootstrap.pypa.io/get-pip.py
	$ sudo python get-pip.py

Install numpy:

.. code-block:: bash

	$ sudo pip install numpy

4. Format and mount the USB drive to store OpenCV
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If you're using a 16 GB (or larger) SD card, skip this step.

If you're using an 8 GB SD card, you will have to download OpenCV to a flash drive, since OpenCV is quite large and won't fit on the SD card. However, once built, the OpenCV library is quite small and can easily fit on the SD card even though the source code may be too large to fit. So, we simply download the OpenCV source code to the flash drive and build it to the Raspberry Pi.

We're going to have to format the flash drive to ext4, which will wipe it, so make sure nothing important is on there. The flash drive has to be formatted, because usually, flash drives don't support symbolic links.

So, plug in the flash drive and run:

.. code-block:: bash

	$ ls /dev/sd*

You should see which directory the USB drive is under. Usually, it's something like "/dev/sda", "/dev/sda1", or "/dev/sda2".

For the rest of this guide, I assume that the directory was "/dev/sda1". Remember, however, to replace "dev/sda1" with the correct directory if yours is different.

To format the USB drive to ext4, which supports symbolic links, use:

.. code-block:: bash

	$ sudo mkfs.ext4 /dev/sda1 -L untitled

Now, we will mount the USB to the RPi so that you can access it.

First, make the directory you're going to mount to.

.. code-block:: bash

	$ sudo mkdir -p /mnt/usb

Then, mount:

.. code-block:: bash

	$ sudo mount /dev/sda1 /mnt/usb

5. Download and extract the OpenCV source code
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If you followed Step 4, then change directories to your USB drive.

.. code-block:: bash

	$ cd /mnt/usb

If you didn't have to, then change to your home directory.

.. code-block:: bash

	$ cd ~

Then, download and extract OpenCV.

.. code-block:: bash

	$ wget -O opencv.zip https://github.com/Itseez/opencv/archive/3.2.0.zip
	$ unzip opencv.zip
	$ wget -O opencv_contrib.zip https://github.com/Itseez/opencv_contrib/archive/3.2.0.zip
	$ unzip opencv_contrib.zip

These commands will take a while depending on your internet connection since OpenCV is quite large.

6. Prepare to build OpenCV
^^^^^^^^^^^^^^^^^^^^^^^^^^

If you followed step 4, use:

.. code-block:: bash

	$ cd /mnt/usb/opencv-3.2.0/

Otherwise, use:

.. code-block:: bash

	$ cd ~/opencv-3.2.0/

Prepare to build OpenCV with these options:

.. code-block:: bash

	$ mkdir build
	$ cd build
	$ cmake -D CMAKE_BUILD_TYPE=RELEASE \
		-D CMAKE_INSTALL_PREFIX=/usr/local \
		-D INSTALL_C_EXAMPLES=OFF \
		-D INSTALL_PYTHON_EXAMPLES=ON \
		-D OPENCV_EXTRA_MODULES_PATH=~/opencv_contrib-3.2.0/modules \
		-D BUILD_EXAMPLES=ON \
		-D ENABLE_NEON=ON ..

To enter arguments on a newline in the terminal as shown above, press the \ key and then press enter. Note that you can just enter all those arguments on one line, however messy that may be.

7. Build OpenCV
^^^^^^^^^^^^^^^

It's recommended to let this run overnight, since it takes a LONG time (4 or more hours).

The safest option is to use:

.. code-block:: bash

	$ sudo make

If you want to use multiple cores (to speed things up), use:

.. code-block:: bash

	$ sudo make -j3

This may fail sometimes, however. If the process fails, use:

.. code-block:: bash

	$ sudo make clean

and try again.

8. Install the build
^^^^^^^^^^^^^^^^^^^^

This step also takes a while, but not nearly as long as building.

.. code-block:: bash

	$ sudo make install
	$ sudo ldconfig

9. Configure OpenCV
^^^^^^^^^^^^^^^^^^^

Edit the OpenCV config file:

.. code-block:: bash

	$ sudo nano /etc/ld.so.conf.d/opencv.conf

The file will be blank, so add to the bottom of the file:

.. code-block:: none

	/usr/local/lib

with a blank line at the end.

Use Ctrl-O, then Ctrl-X to save and exit.

.. code-block:: bash

	$ sudo ldconfig

Now, configure bashrc:

.. code-block:: bash

	$ sudo nano /etc/bash.bashrc

Add these lines at the bottom:

.. code-block:: none

	PKG_CONFIG_PATH=$PKG_CONFIG_PATH:/usr/local/lib/pkgconfig       
	export PKG_CONFIG_PATH

Add a blank line at the bottom, and once again, Ctrl-O and Ctrl-X to save and exit.

Now, reboot.

.. code-block:: bash

	$ sudo reboot now

10. Check to make sure it worked
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

When you use:

.. code-block:: bash

    $ python
    >>> import cv2
    >>> print cv2.__version__

it should print out '3.2.0'. If it did not, then something went wrong, and recheck your steps.

If it worked, then you've successfully installed OpenCV on the RPi. You may want to make a backup image of the SD Card using a tool like `Win32 Disk Imager <https://thepihut.com/blogs/raspberry-pi-tutorials/17789160-backing-up-and-restoring-your-raspberry-pis-sd-card>`_ so that you never have to go through this extremely long process again.

Installing the Picamera
-----------------------

f you're using a RPi and OpenCV, you're probably using a Raspberry Pi Camera. This means that you must also install the Picamera.

Firstly, plug in the camera.

Then, use raspi-config and enable the camera:

.. code-block:: bash

	$ sudo raspi-config

.. image:: ./_static/raspi-config.png
   :width: 100%

Then, select Enable Camera, and enable it.

To test the camera, use:

.. code-block:: bash

	$ raspistill -t 5000 -o image.jpg

This shows a preview of the camera feed for 5 seconds, then takes a picture and saves it to image.jpg.

Then, if the picamera works, install the python package:

.. code-block:: bash

	$ sudo pip install picamera

	$ sudo pip install "picamera[array]"

After that's done, then the entire installation process is complete. Hooray! I hope you never have to do that again.

.. toctree::
	:glob:
	:maxdepth: 10
	:caption: Contents

