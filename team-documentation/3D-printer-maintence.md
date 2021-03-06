# 3D Printer Maintence Documentation

<h2 id="firmware-upgrade">Upgrading Firmware</h2>

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

### Firmware Upgrade Process Checklist

1.  Connect the 3D printer you want to upgrade directly to a computer running
    Cura via USB.
2.  If new firmware is available Cura should prompt you when it detects the
    printer. You might have to go to `Settings -> Printers -> Manage Printers`
		and select the printer type you are trying to upgrade in order for it to
		show up. You may have to add the printer using the add printer button if
		you have not done so already. (See [Printer Settings](#printer-settings)
		below for our printers' specific configurations)
3.  Record the `E-Steps` value of the printer. Use the steps at
    [Viewing `E-Steps`](#viewing-esteps) below to view the value, and record it
    either in a text document or on paper.
4.  Record the `Z-Offset` value of the printer. Use the steps at
    [Viewing `Z-Offset`](#viewing-zoffset) below to view the value, and record
		it either in a text document or on paper.
5.  Go to `Settings -> Printers -> Manage Printers` in Cura and select the
    printer type you are trying to upgrade. You may have to add the printer
		using the add printer button if you have not done so already. (See [Printer
		Settings](#printer-settings) below for our printers' specific
		configurations)
6.  Hit the `Upgrade Firmware` button.
7.  Hit `Automatically Upgrade Firmware` after making sure `Update EEPROM` is checked.
8.  Wait until the prompt says `Firmware Update Complete`.
9.  Close the upgrade windows and open the console back up.
10. [Restore the `E-Steps` value](#setting-esteps) using the values you recorded earlier.
11. [Restore the `Z-Offset` value](#setting-zoffset) using the values you recorded earlier.
12. Reconnect the 3D printer to the Raspberry Pi.


<h2 id="toolhead-swap">Changing Extruder Heads</h2>

These instructions were modified from those found on
[LulzBot's OHAI](https://ohai.lulzbot.com/). Look at their instructions as well
if you have questions.

**IMPORTANT:**

Before following any of the below instructions, first:

1.  Home the 3D printer, then move the Z axis to at least 200mm off the bed
    and the X axis to the approximate center of the bed. This will allow for
		ease of access.
2.  Unplug the Power cord and USB cord from the printer.

<h3 id="removing-old-heads">Removing Old Version Single Extruder Heads</h3>

1.  Unplug the tool head connector from the wiring harness.
2.  Be sure to support the tool head with your hand to prevent it from falling
    onto the print surface.
3.  Unscrew the tool head mounting screw by turning counter-clockwise from the
    X-axis carriage with your 2.5 mm hex key.
4.  Verify that the `E-Steps` value is recorded on the back of the extruder.
    If it is not, then remount and reconnect the extruder to the printer, plug
		things back in and follow the instructions in
		[Recording `E-Steps`](#viewing-esteps) to view the E-Steps for the head.
		Make sure to write them down on the back of the extruder assembly.

<h3 id="installing-taz-aerostruder">Installing an Aerostruder Head on A Taz 6 printer</h3>

**NOTE:** `E-Steps` values for Aerostruder heads are preset in the firmware of
the 3D printer. Make sure to [Upgrade the firmware](#firmware-upgrade) of the
printer before installing the new extruder.

1.  Remove existing extruder head if installed.
2.  Seat the new tool head into the X-axis carriage, allowing the tool head to
    self-center.
3.  Support the tool head with your hand to prevent it from falling onto the
    print surface.
4.  Secure the tool head to the X-axis carriage with the M3 screw from the
    earlier step, screwing it in a 2.5 mm hex key.
5.  Pull foam off of 16-pin connector piece.
6.  Find the side of the connector piece that has a small arrow and align it
    with the same side on the other connection piece.
7.  Connect the two 16-pin connectors, making sure to match them based on the
    pins in the connector.
8.  Extract Factory Filament if present by heating extruder to 205 and using
    the retract function of Cura or Octoprint to remove the filament.
		(Do not pull it manually as this can clog the extruder.)

<h3 id="installing-taz-oldhead">Installing the Old Taz Heads</h3>

1.  Remove existing extruder head if installed.
2.  Seat the new tool head into the X-axis carriage, allowing the tool head to
    self-center.
3.  Support the tool head with your hand to prevent it from falling onto the
    print surface.
4.  Secure the tool head to the X-axis carriage with the M3 screw from the
    earlier step, screwing it in a 2.5 mm hex key.
6.  Find the side of the connector piece that has a small arrow and align it
    with the same side on the other connection piece.
7.  Connect the two 16-pin connectors, making sure to match them based on the
    pins in the connector.




<h2 id="refs">References</h2>

<h3 id="viewing-esteps">Viewing the <code>E-Steps</code> Value</h3>

1.  Power on your LulzBot 3D printer and connect through USB to your computer.
2.  Open Cura LulzBot Edition.
3.  Press the 3D printer icon to switch to the printer interface.
4.  In the Manual control section press the Connect button.
5.  Press the Console button to open the command console window.
6.  Type `M501` in the terminal window and hit enter. Make sure to capitalize
    the "M". This command shows the EEPROM settings. This will also show your
		Z-Offset if you are upgrading firmware.
7.  Find the line following Steps per unit
8.  Record your Extruder steps per unit: `E___.__`
9.  Close the control window.

Verify these instructions are current at the following link:
<https://www.lulzbot.com/learn/tutorials/firmware-flashing-through-cura#get-esteps>

<h3 id="viewing-zoffset">Viewing the <code>Z-Offset</code> Value</h3>

1.  Power on your LulzBot 3D printer and connect through USB to your computer.
2.  Open Cura LulzBot Edition.
3.  Press the 3D printer icon to switch to the printer interface.
4.  In the Manual control section press the Connect button.
5.  Press the Console button to open the command console window.
6.  Type `M851` in the terminal window and hit enter. Make sure to capitalize
    the "M". This command shows the Z offset.
7.  Record your Z Offset: `Z-X.XX`
8.  Close the control window.

Verify these instructions are current at the following link:
<https://www.lulzbot.com/learn/tutorials/Z-axis-offset#get-offset>

<h3 id="setting-esteps">Setting the <code>E-Steps</code> Value</h3>

1.  Power on your LulzBot 3D printer and connect through USB to your computer.
2.  Open Cura LulzBot Edition.
3.  Press the 3D printer icon to switch to the printer interface.
4.  In the Manual control section press the Connect button.
5.  Press the Console button to open the command console window.
6.  Type `M92 EXXX` in the terminal window, with the `XXX` replaced by the
    `E-Step` value you are setting and hit enter. Make sure to capitalize
    the "M". This command sets the `E-Step` value in the firmware.
7.  Type `M500` in the terminal window and hit enter. Make sure to capitalize
    the "M". This command saves the settings in the Flash memory.
8.  Close the control window.

Verify these instructions are current at the following link:
<https://www.lulzbot.com/learn/tutorials/firmware-flashing-through-cura#esteps>

<h3 id="setting-zoffset">Settin the <code>Z-Offset</code> Value</h3>

1.  Power on your LulzBot 3D printer and connect through USB to your computer.
2.  Open Cura LulzBot Edition.
3.  Press the 3D printer icon to switch to the printer interface.
4.  In the Manual control section press the Connect button.
5.  Press the Console button to open the command console window.
6.  Type `M851 Z-X.XX` in the terminal window with `X.XX` replaced with the
     value you are setting and hit enter. Make sure to capitalize the "M". This
    command sets the Z offset.
7.  Type `M500` in the terminal window and hit enter. Make sure to capitalize
    the "M". This command saves the settings in the Flash memory
8.  Close the control window.

Verify these instructions are current at the following link:
<https://www.lulzbot.com/learn/tutorials/Z-axis-offset#restore-offset>

<h3 id="printer-settings">Lulzbot Printer Settings</h3>

<h4 id="taz-settings">Taz 6 Settings</h4>

In the `Add Printer` Dialog of Cura:

*   Printer = LulzBot Taz 6
*   Toolhead = Single Extruder (If using the old extruders)
*   Toolhead = Aerostruder V1 (If using the new Aerostruder heads)
*   Toolhead = MOARstruder (If using the MOARstruder)
*   Toolhead = DualExtruer V2 (If using the DualExtruer)
*   Graphical LCD = Yes

<h4 id="mini-settings">Mini Settings</h4>

In the `Add Printer` Dialog of Cura:

*   Printer = LulzBot Mini
*   Toolhead = Single Extruder (Normally)
*   Toolhead = Flexistruder V2 (If using the Flexistruder)
*   Graphical LCD = No
