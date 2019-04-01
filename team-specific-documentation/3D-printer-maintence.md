# 3D Printer Maintence Documentation

## Upgrading Firmware

The firmware of the internal controllers of **ALL** the 3D printers should be
upgraded at the begining of each semester, as well as when upgrading to a new
Cura version or implementing major changes to the printers such as upgrading the
extruder heads.

The easiest way to check for new firmware is to plug the 3D printer you want to
upgrade directly into a computer with the most recent version of [Cura Lulzbot
Edition](https://www.lulzbot.com/cura#download) on it. This is easiest to do
with a personal laptop as it is more portable than the lab computers. When you
connect, Cura should automatically notify you that there is a firmware upgrade
available.

Before hitting the upgrade button, you must first record both the `E-Steps` and
the `Z-Offset` parameters which are stored in the printer firmware. These get
overwritten during the firmware upgrade process and must be restored manually.

To record the `E-Steps` value:

1.  Power on your LulzBot 3D printer and connect through USB to your computer.
2.  Open Cura LulzBot Edition.
3.  Press the 3D printer icon to switch to the printer interface.
4.  In the Manual control section press the Connect button.
5.  Press the Console button to open the command console window.
6.  Send the View EEPROM settings command, making sure to capitalize the "M": `M501`
7.  Find the line following Steps per unit
8.  Record your Extruder steps per unit: `E___.__`
9.  Close the control window.

Verify these instructions are current at the following link: <https://www.lulzbot.com/learn/tutorials/firmware-flashing-through-cura#get-esteps>
