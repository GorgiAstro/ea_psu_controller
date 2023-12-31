A clone of the `ea_psu_controller` code (version 1.1.0) because it is not available on GitHub anymore, only on PyPi. Source: https://pypi.org/project/ea-psu-controller/ 

A simple usage example is available in the Jupyter notebook `./example.ipynb`.

SOMANET Test Suite - Controller for EA PSUs
========================================

Linux
=====

Recommended step
----------------
Move ``99-ea-psu.rules`` to ``/etc/udev/rules.d/`` and restart ``udev`` with ``sudo service udev restart``.
This rule will create devices, that look like ``ea-ps-20xx-xx-0`` or ``ea-ps-23xx-xx-0`` with an increasing counter for every
connected device.

Before start
------------
To use ``ttyACMx`` devices without sudo, add your user to the group ``dialout``:

``sudo adduser your_user dialout``


Windows
=======
When connecting the power supply, Windows will install automatically the correct drivers.

Installing module
-----------------
``pip3 install ea_psu_controller``

Import module
-------------
``import ea_psu_controller``

Create object
-------------
To connect to a particular PSU, you can call

Linux: ``psu = PsuEA(comport='ttyACM0')``

Windows: ``psu = PsuEA(comport='COM1')`` or as com port description: ``psu = PsuEA(comport='PS 2000 B')``

If you added the device rule and you connected only one PSU, there is no need to provide a device name:

``psu = PsuEA()``

Also possible is:

``psu = PsuEA(comport='ea-ps-20xx-xx-0')``

| If there is more then one PSU connected to the host, the script will connect to the first device found.
| It is also possible to take the S/N written on the back of the PSU and call (dev rule on Linux needed):

``psu = PsuEA(sn='0123456789')``

or to use the device designator (dev rule on Linux needed):

``psu = PsuEA(desi='PS 2142-10B')``


Dis/connect to power supply for controlling
---------------------------
``psu.remote_on()``

``psu.remote_off()``

For multi output devices most functions provide a additional output argument:

``psu.remote_on(output_num=0)`` or ``psu.remote_on(output_num=1)``

| It's only necessary to call ``remote_on()``, when you want to control the PSU.
| If you just want to read device information, you don't need to.

Power on and off output
-----------------------
``psu.output_on()``

``psu.output_off()``

or

``psu.output_on(output_num=1)``

``psu.output_off(output_num=1)``

Set parameters
--------------
Arguments can be int or float.

``psu.set_voltage(24)``

``psu.set_current(0.5)``

``psu.set_ovp(30)``

``psu.set_ocp(8)``

The script will always set the maximum possible values in dependency of the nominal power.

| For example:
| Nominal power = 160 W
| If you set now the voltage to 40 V, it's not possible to set a higher current than 4 A (=160W/40V).
| If you want to set a higher current, you need to decrease first the voltage.


Get parameters
--------------
Return argument: float.

``psu.get_voltage()``

``psu.get_current()``

Maximum sampling rate is ~10 Hz.

Get status
--------------
Return argument: dictionary

``psu.get_status()``

Dict contains following keys:

- 'remote on'
- 'output on'
- 'controller state' (CV=0, CC=2)
- 'tracking active'
- 'OVP activ'
- 'OCP activ'
- 'OPP activ'
- 'OTP activ'

Get device description
--------------
Return argument: tuple (name, SN)

``psu.get_device_description()``


Close connection
--------------
To close the connection, call:

``psu.close(remote=False, output=False)``

Set both arguments to True to turn off the output and remote control
