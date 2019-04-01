# OctoPrint Setup Reference

This is referenced against OctoPrint 1.3.10 and OctoPi 0.16.0.

OctoPrint contains a very basic implementation of access control which we are
currently using. There is development of a better access control system in the
works, so some of these instructions might need to change.

**Important:** These instructions are fairly complete but assume some basic
linux knowlege as well as familararity with computers and networking. I suggest
googling anything you don't understand.

## Create SD card image (only necessary if setting up new pi or old image gets corrupted)

-   Download SD image
-   Copy to SD card using system specific tooling

### On First boot:

-   Connect keyboard and HDMI monitor. Do not connect ethernet cable yet
-   Login as user pi and change password using `passwd` command. See password document
-   Set static IP to one of list from ENS.
-   Edit `/etc/dhcpcd.conf` file using command `sudo nano /etc/dhcpcd.conf`.
-   Uncomment and correct lines listed under example static IP config. See example files.
-   Reboot
-   Make sure to turn on ntp client:
-   For recent distributions, the steps are as follows:
    -   `sudo nano /etc/systemd/timesyncd.conf` and set server to `ntp.colostate.edu`
    -   `sudo timedatectl set-ntp True`
    -   `sudo reboot`
    -   `sudo timedatectl status` to verify
-   Change hostname
    -   Edit `/etc/hostname` and change octopi to the dns name of the pi
    -   Edit `/etc/hosts` and make sure that there are at least two lines
          127.0.0.1 localhost.localdomain localhost
          127.0.1.1 dns-name-of-pi

Now connect via ssh either to static IP or hostname.

Configure locale and timezone via `sudo raspi-config`

Now go to the DNS name assigned to the pi in a web browser on another computer
and go through the initial configuration wizard, create an initial admin account
called `b111-admin` with the password on the password document. Leave everything
else default for now.

## SSH Setup

By default, you can ssh into the Raspberry Pi using the hostname, while you are
on campus.

A typical command line on MacOS or Linux would look something like:

```bash
ssh pi@b111-taz-printers.engr.colostate.edu # username@server
```

After hitting enter, you will be prompted for the pi user's password.

If you are using windows, you can either install a terminal emulator software
such as PuTTy or install Git Bash, which adds a better terminal to windows and
basic linux commands including ssh. This way you can use the linux instructions
with no worries.

### Passwordless SSH Setup (Optional but highly recommended)

**Important:** If you know how to use SSH and keys etc, ignore this section.

In order to simplify connecting via SSH to the Pi's you can set up SSH Keys
which use public private key encryption and authentication rather than typing
passwords.

Please learn more about using ssh keys including agents before working through
this section.

You must perform these steps on every computer you intend to use to connect to
the Pi's with no password. Access with a password will still be enabled so you
don't need to do this on every computer. I recommend you set this up on your
personal computer as well as on the ENS computers in your U drive. This way you
can easily connect from any engineering computer.

These steps only work on Mac and Linux by default. (windows needs some additional
configuration)

1.  Check for existing keys in `~/.ssh` on the computer you will be connecting
    from. These will be files of the form `id_*` where \* would be `rsa`,
    `ed25519` or something else.
2.  If there are existing keys there, it is usually easier to use them rather
    than creating a new key.
3.  If there are no keys, you will need to run the command below to generate a
    new public-private keypair.
    `ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519 -q -P ""`. If you want extra
    security, you can change the `""` after the `-P` to a passphrase enclosed in
    the quotes but it is not necessary.
4.  run the command `ssh-copy-id pi@b111-taz-printers.engr.colostate.edu` and
    `ssh-copy-id pi@b111-mini-printers.engr.colostate.edu`

## Configuring multiple printers on one pi:

This is adapted from
<http://thomas-messmer.com/index.php/14-free-knowledge/howtos/79-setting-up-octoprint-for-multiple-printers>

USB devices are named by linux in the order they are detected by the kernel,
which means that the `/dev/ttyAMC0` device can actually refer to a different
printer when things are unplugged or the pi is rebooted.

The solution to this is to configure udev rules to automatically map the
printers to a static symlink.

First, find the differences between the two devices using the udevadm command:
`udevadm info -a -n /dev/ttyACM1 | less`

The pipe into less allows you to scroll through the command output easily on a
short terminal.

>Udevadm info starts with the device specified by the devpath and then walks up
>the chain of parent devices. It prints for every device found, all possible
>attributes in the udev rules key format. A rule to match, can be composed by
>the attributes of the device and the attributes from one single parent device.
>Although the tty subsystem is still available with usb ATTRS. This was a
>mistake I made.

Example output is shown below:

```bash
pi@b111-taz-printers:~ $ udevadm info -a -n /dev/ttyACM1

  looking at device '/devices/platform/soc/3f980000.usb/usb1/1-1/1-1.4/1-1.4:1.0/tty/ttyACM1':
    KERNEL=="ttyACM1"
    SUBSYSTEM=="tty"
    DRIVER==""

  looking at parent device '/devices/platform/soc/3f980000.usb/usb1/1-1/1-1.4/1-1.4:1.0':
    KERNELS=="1-1.4:1.0"
    SUBSYSTEMS=="usb"
    DRIVERS=="cdc_acm"
    ATTRS{authorized}=="1"
    ATTRS{bAlternateSetting}==" 0"
    ATTRS{bInterfaceClass}=="02"
    ATTRS{bInterfaceNumber}=="00"
    ATTRS{bInterfaceProtocol}=="01"
    ATTRS{bInterfaceSubClass}=="02"
    ATTRS{bNumEndpoints}=="01"
    ATTRS{bmCapabilities}=="6"
    ATTRS{supports_autosuspend}=="1"

  looking at parent device '/devices/platform/soc/3f980000.usb/usb1/1-1/1-1.4':
    KERNELS=="1-1.4"
    SUBSYSTEMS=="usb"
    DRIVERS=="usb"
    ATTRS{authorized}=="1"
    ATTRS{avoid_reset_quirk}=="0"
    ATTRS{bConfigurationValue}=="1"
    ATTRS{bDeviceClass}=="02"
    ATTRS{bDeviceProtocol}=="00"
    ATTRS{bDeviceSubClass}=="00"
    ATTRS{bMaxPacketSize0}=="8"
    ATTRS{bMaxPower}=="100mA"
    ATTRS{bNumConfigurations}=="1"
    ATTRS{bNumInterfaces}==" 2"
    ATTRS{bcdDevice}=="0001"
    ATTRS{bmAttributes}=="c0"
    ATTRS{busnum}=="1"
    ATTRS{configuration}==""
    ATTRS{devnum}=="5"
    ATTRS{devpath}=="1.4"
    ATTRS{devspec}=="  (null)"
    ATTRS{idProduct}=="0001"
    ATTRS{idVendor}=="27b1"
    ATTRS{ltm_capable}=="no"
    ATTRS{manufacturer}=="UltiMachine (ultimachine.com)"
    ATTRS{maxchild}=="0"
    ATTRS{product}=="RAMBo"
    ATTRS{quirks}=="0x0"
    ATTRS{removable}=="removable"
    ATTRS{serial}=="755303132313515092B0"
    ATTRS{speed}=="12"
    ATTRS{urbnum}=="11"
    ATTRS{version}==" 1.10"

  looking at parent device '/devices/platform/soc/3f980000.usb/usb1/1-1':
    KERNELS=="1-1"
    SUBSYSTEMS=="usb"
    DRIVERS=="usb"
    ATTRS{authorized}=="1"
    ATTRS{avoid_reset_quirk}=="0"
    ATTRS{bConfigurationValue}=="1"
    ATTRS{bDeviceClass}=="09"
    ATTRS{bDeviceProtocol}=="02"
    ATTRS{bDeviceSubClass}=="00"
    ATTRS{bMaxPacketSize0}=="64"
    ATTRS{bMaxPower}=="2mA"
    ATTRS{bNumConfigurations}=="1"
    ATTRS{bNumInterfaces}==" 1"
    ATTRS{bcdDevice}=="0200"
    ATTRS{bmAttributes}=="e0"
    ATTRS{busnum}=="1"
    ATTRS{configuration}==""
    ATTRS{devnum}=="2"
    ATTRS{devpath}=="1"
    ATTRS{idProduct}=="9514"
    ATTRS{idVendor}=="0424"
    ATTRS{ltm_capable}=="no"
    ATTRS{maxchild}=="5"
    ATTRS{quirks}=="0x0"
    ATTRS{removable}=="unknown"
    ATTRS{speed}=="480"
    ATTRS{urbnum}=="45"
    ATTRS{version}==" 2.00"

  looking at parent device '/devices/platform/soc/3f980000.usb/usb1':
    KERNELS=="usb1"
    SUBSYSTEMS=="usb"
    DRIVERS=="usb"
    ATTRS{authorized}=="1"
    ATTRS{authorized_default}=="1"
    ATTRS{avoid_reset_quirk}=="0"
    ATTRS{bConfigurationValue}=="1"
    ATTRS{bDeviceClass}=="09"
    ATTRS{bDeviceProtocol}=="01"
    ATTRS{bDeviceSubClass}=="00"
    ATTRS{bMaxPacketSize0}=="64"
    ATTRS{bMaxPower}=="0mA"
    ATTRS{bNumConfigurations}=="1"
    ATTRS{bNumInterfaces}==" 1"
    ATTRS{bcdDevice}=="0414"
    ATTRS{bmAttributes}=="e0"
    ATTRS{busnum}=="1"
    ATTRS{configuration}==""
    ATTRS{devnum}=="1"
    ATTRS{devpath}=="0"
    ATTRS{idProduct}=="0002"
    ATTRS{idVendor}=="1d6b"
    ATTRS{interface_authorized_default}=="1"
    ATTRS{ltm_capable}=="no"
    ATTRS{manufacturer}=="Linux 4.14.34-v7+ dwc_otg_hcd"
    ATTRS{maxchild}=="1"
    ATTRS{product}=="DWC OTG Controller"
    ATTRS{quirks}=="0x0"
    ATTRS{removable}=="unknown"
    ATTRS{serial}=="3f980000.usb"
    ATTRS{speed}=="480"
    ATTRS{urbnum}=="26"
    ATTRS{version}==" 2.00"

  looking at parent device '/devices/platform/soc/3f980000.usb':
    KERNELS=="3f980000.usb"
    SUBSYSTEMS=="platform"
    DRIVERS=="dwc_otg"
    ATTRS{busconnected}=="Bus Connected = 0x1"
    ATTRS{buspower}=="Bus Power = 0x1"
    ATTRS{bussuspend}=="Bus Suspend = 0x0"
    ATTRS{devspeed}=="Device Speed = 0x0"
    ATTRS{driver_override}=="(null)"
    ATTRS{enumspeed}=="Device Enumeration Speed = 0x1"
    ATTRS{fr_interval}=="Frame Interval = 0x1d4b"
    ATTRS{ggpio}=="GGPIO = 0x00000000"
    ATTRS{gnptxfsiz}=="GNPTXFSIZ = 0x01000306"
    ATTRS{gotgctl}=="GOTGCTL = 0x001c0001"
    ATTRS{gpvndctl}=="GPVNDCTL = 0x00000000"
    ATTRS{grxfsiz}=="GRXFSIZ = 0x00000306"
    ATTRS{gsnpsid}=="GSNPSID = 0x4f54280a"
    ATTRS{guid}=="GUID = 0x2708a000"
    ATTRS{gusbcfg}=="GUSBCFG = 0x20001700"
    ATTRS{hcd_frrem}=="HCD Dump Frame Remaining"
    ATTRS{hcddump}=="HCD Dump"
    ATTRS{hnp}=="HstNegScs = 0x0"
    ATTRS{hnpcapable}=="HNPCapable = 0x1"
    ATTRS{hprt0}=="HPRT0 = 0x00001005"
    ATTRS{hptxfsiz}=="HPTXFSIZ = 0x02000406"
    ATTRS{hsic_connect}=="HSIC Connect = 0x1"
    ATTRS{inv_sel_hsic}=="Invert Select HSIC = 0x0"
    ATTRS{mode}=="Mode = 0x1"
    ATTRS{mode_ch_tim_en}=="Mode Change Ready Timer Enable = 0x0"
    ATTRS{rd_reg_test}=="Time to read GNPTXFSIZ reg 10000000 times: 1680 msecs (168 jiffies)"
    ATTRS{regdump}=="Register Dump"
    ATTRS{regoffset}=="0xffffffff"
    ATTRS{regvalue}=="invalid offset"
    ATTRS{rem_wakeup_pwrdn}==""
    ATTRS{remote_wakeup}=="Remote Wakeup Sig = 0 Enabled = 0 LPM Remote Wakeup = 0"
    ATTRS{spramdump}=="SPRAM Dump"
    ATTRS{srp}=="SesReqScs = 0x1"
    ATTRS{srpcapable}=="SRPCapable = 0x1"
    ATTRS{wr_reg_test}=="Time to write GNPTXFSIZ reg 10000000 times: 350 msecs (35 jiffies)"

  looking at parent device '/devices/platform/soc':
    KERNELS=="soc"
    SUBSYSTEMS=="platform"
    DRIVERS==""
    ATTRS{driver_override}=="(null)"

  looking at parent device '/devices/platform':
    KERNELS=="platform"
    SUBSYSTEMS==""
    DRIVERS==""
```

This command prints info about all the links in the usb chain on the pi itself,
starting with the chosen device. Each “looking at parent device” like indicates
a different level. They are arranged from deepest to shallowest level. For our
environment, I found that the only difference that mattered was the
`ATTRS{serial}` line in the `SUBSYSTEMS=="usb", DRIVERS==”usb”` line. When
creating the udev file below, you need to keep all the attributes that you are
matching within one “parent device” section.

Now you need to create a file called `99-usb-serial.rules` in
`/etc/udev/rules.d` using the command: `sudo nano
/etc/udev/rules.d/99-usb-serial.rules`

This will open up a simple editor that works similarly to notepad or textedit.
Add a line similar to the one below for each printer, verifying that all the
information is correct.

This version will work, but connects to the wrong device.
```
SUBSYSTEM=="usb", ATTRS{idVendor}=="27b1", ATTRS{idProduct}=="0001", ATTRS{serial}=="755303132313515092B0", SYMLINK+="ttyTAZ2"
```

Even though the idVendor, idProduct and serial attrs are not part of the tty
device section, we can still use them. This maps to the tty serial port rather
than the usb device itself.

```
SUBSYSTEM=="tty", ATTRS{idVendor}=="27b1", ATTRS{idProduct}=="0001", ATTRS{serial}=="755303132313515092B0", SYMLINK+="ttyTAZ2"
```

The symlink is what you want the printer to be called and will appear under /dev/.

The current udev rules that I created are documented below. These should work
unless something changes with the printers. Verify these values before using
them.

B111-taz-printers:

```
SUBSYSTEM=="tty", ATTRS{idVendor}=="27b1", ATTRS{idProduct}=="0001", ATTRS{serial}=="5553933303835141C011", SYMLINK+="ttyTAZ1"
SUBSYSTEM=="tty", ATTRS{idVendor}=="27b1", ATTRS{idProduct}=="0001", ATTRS{serial}=="755303132313515092B0", SYMLINK+="ttyTAZ2"
```

B111-mini-printers:

```
SUBSYSTEM=="tty", ATTRS{idVendor}=="27b1", ATTRS{idProduct}=="0001", ATTRS{serial}=="740343139383517070F1", SYMLINK+="ttyLM2"
SUBSYSTEM=="tty", ATTRS{idVendor}=="27b1", ATTRS{idProduct}=="0001", ATTRS{serial}=="74035323434351B01232", SYMLINK+="ttyLM1"
```
(LM stands for  Lulzbot Mini)

Now you need to create a new instance of octoprint for each printer connected to
a pi. If there are more than 2 printers connected, you will need to repeat these
steps for each additional printer, replacing `octoprint2` with `octoprintn`
where n is the number of the printer. (the n can be anything descriptive of
which printer is being configured, but it really doesn’t matter)

First, copy octoprint directory: `cp -R /home/pi/.octoprint/ /home/pi/.octoprint2`

Then copy configuration script: `sudo cp /etc/default/octoprint /etc/default/octoprint2`

Now edit the configuration script that you just created: `sudo nano /etc/default/octoprint2`

You need to increment the port that the DAEMON runs on, so for the second
printer, set `PORT=5001`, and add the basedir option to the `DAEMON_ARGS` line:
`DAEMON_ARGS="--host=$HOST --port=$PORT --basedir /home/pi/.octoprint2/"`

In order to prevent confusion, also edit the original config script: `sudo nano /etc/default/octoprint`

Add the basedir option to the `DAEMON_ARGS` line:
`DAEMON_ARGS="--host=$HOST --port=$PORT --basedir /home/pi/.octoprint/"`

Now you need to copy the init script and modify it:
```bash
sudo cp /etc/init.d/octoprint /etc/init.d/octoprint2
sudo nano /etc/init.d/octoprint2
```

Change every instance of octoprint to octoprint2 except for the
`DAEMON=/usr/bin/octoprint` line. The things you need to change should all be at
the top of the file. No need to scroll down below the SCRIPTNAME line. See an
example below.

```bash
#!/bin/sh

### BEGIN INIT INFO
# Provides:          octoprint2
# Required-Start:    $local_fs networking
# Required-Stop:
# Should-Start:
# Should-Stop:
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# Short-Description: OctoPrint daemon
# Description:       Starts the OctoPrint daemon with the user specified in
#                    /etc/default/octoprint2.
### END INIT INFO

# Author: Sami Olmari

PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
DESC="OctoPrint2 Daemon"
NAME="OctoPrint2"
DAEMON=/usr/bin/octoprint
PIDFILE=/var/run/$NAME.pid
PKGNAME=octoprint2
SCRIPTNAME=/etc/init.d/$PKGNAME
```

Now this is where we deviate from the linked tutorial a bit. We are going to
create some symlinks between the .octoprint folders so things like gcode files,
printer profiles and snapshots are shared between the two printers. We are also
going to link the users file so that setup will be simpler and you will only
need to change users on each pi once rather than twice.


First we need to remove the directories we want to duplicate in the new
.octoprint folder: .octoprint2. You need to use `rm -rf` to delete the timelapse
folder since it will not be empty

```bash
rmdir /home/pi/.octoprint2/uploads
rm -rf /home/pi/.octoprint2/timelapse
rm /home/pi/.octoprint2/users.yaml
rm -rf /home/pi/.octoprint2/printerProfiles
```
Now create the symlinks
```bash
ln -sf /home/pi/.octoprint/uploads/ /home/pi/.octoprint2/uploads
ln -sf /home/pi/.octoprint/timelapse/ /home/pi/.octoprint2/timelapse
ln -sf /home/pi/.octoprint/users.yaml /home/pi/.octoprint2/users.yaml
ln -sf /home/pi/.octoprint/printerProfiles/ /home/pi/.octoprint2/printerProfiles
```
You can check your work by running the command `ls -lash /home/pi/.octoprint2`

The files and directories you linked should have arrows next to them pointing to
the .octoprint directory.

After making the changes in the .octoprint2 directory, we need to inform the
operating system about the new daemon(s) we just created.

```bash
sudo systemctl daemon-reload

# And we want it to run automatically on bootup:

sudo update-rc.d octoprint2 defaults

# Now start the service manually for now to test it:

sudo systemctl start octoprint2

# And see if it is running with no errors:

sudo systemctl status octoprint2.service
```

Now we need to allow access to both octoprint instances. We will set it up so
our printers are accessible at subdirectories under the main domain name. This
is done using a service called HaProxy that is already in use in order to
translate the port 5000 webserver of the original octoprint service to port 80
which is the automatic web port.

First we need to stop the HaProxy service. This will temporarily stop the web
interface from being accessible.

```bash
sudo systemctl stop haproxy
```
Now make a copy of the existing config file as a backup and then edit the config file:

```bash
sudo cp /etc/haproxy/haproxy.cfg /etc/haproxy/haproxy.old
sudo nano /etc/haproxy/haproxy.cfg
```

You will need to create a new backend config section for each new printer you
are adding, as well as edit the existing one. The frontend config file will also
need to be edited. See examples below for the haproxy-config I implemented for
each of the 2 Raspberry Pis. You should just be able to copy and paste unless
the HaProxy config format has seriously changed.

### Mini Printer Config

```config
global
        maxconn 4096
        user haproxy
        group haproxy
        log 127.0.0.1 local1 debug

defaults
        log     global
        mode    http
        option  httplog
        option  dontlognull
        retries 3
        option redispatch
        option http-server-close
        option forwardfor
        maxconn 2000
        timeout connect 5s
        timeout client  15min
        timeout server  15min

frontend public
        bind :::80 v4v6
        bind :::443 v4v6 ssl crt /etc/ssl/snakeoil.pem
        option forwardfor except 127.0.0.1
        use_backend webcam if { path_beg /webcam/ }
        use_backend mini1 if { path_beg /mini1/ }
        use_backend mini2 if { path_beg /mini2/ }
        default_backend webcam

backend mini1
        acl needs_scheme req.hdr_cnt(X-Scheme) eq 0
        reqrep ^([^\ :]*)\ /mini1/(.*) \1\ /\2
        option forwardfor
        server octoprint1 127.0.0.1:5000
        reqadd X-Scheme:\ https if needs_scheme { ssl_fc }
        reqadd X-Scheme:\ http if needs_scheme !{ ssl_fc }
        reqadd X-Script-Name:\ /mini1

backend mini2
        acl needs_scheme req.hdr_cnt(X-Scheme) eq 0
        reqrep ^([^\ :]*)\ /mini2/(.*) \1\ /\2
        option forwardfor
        server octoprint1 127.0.0.1:5001
        reqadd X-Scheme:\ https if needs_scheme { ssl_fc }
        reqadd X-Scheme:\ http if needs_scheme !{ ssl_fc }
        reqadd X-Script-Name:\ /mini2

#backend octoprint
#        acl needs_scheme req.hdr_cnt(X-Scheme) eq 0
#
#        reqrep ^([^\ :]*)\ /(.*) \1\ /\2
#        reqadd X-Scheme:\ https if needs_scheme { ssl_fc }
#        reqadd X-Scheme:\ http if needs_scheme !{ ssl_fc }
#        option forwardfor
#        server octoprint1 127.0.0.1:5000
#        errorfile 503 /etc/haproxy/errors/503-no-octoprint.http

backend webcam
        reqrep ^([^\ :]*)\ /webcam/(.*)     \1\ /\2
        server webcam1  127.0.0.1:8080
        errorfile 503 /etc/haproxy/errors/503-no-webcam.http
```

### Taz Printer Config

```config
global
        maxconn 4096
        user haproxy
        group haproxy
        log 127.0.0.1 local1 debug

defaults
        log     global
        mode    http
        option  httplog
        option  dontlognull
        retries 3
        option redispatch
        option http-server-close
        option forwardfor
        maxconn 2000
        timeout connect 5s
        timeout client  15min
        timeout server  15min

frontend public
        bind :::80 v4v6
        bind :::443 v4v6 ssl crt /etc/ssl/snakeoil.pem
        option forwardfor except 127.0.0.1
        use_backend webcam if { path_beg /webcam/ }
        use_backend taz1 if { path_beg /taz1/ }
        use_backend taz2 if { path_beg /taz2/ }
        default_backend webcam

backend taz1
        acl needs_scheme req.hdr_cnt(X-Scheme) eq 0
        reqrep ^([^\ :]*)\ /taz1/(.*) \1\ /\2
        option forwardfor
        server octoprint1 127.0.0.1:5000
        reqadd X-Scheme:\ https if needs_scheme { ssl_fc }
        reqadd X-Scheme:\ http if needs_scheme !{ ssl_fc }
        reqadd X-Script-Name:\ /taz1

backend taz2
        acl needs_scheme req.hdr_cnt(X-Scheme) eq 0
        reqrep ^([^\ :]*)\ /taz2/(.*) \1\ /\2
        option forwardfor
        server octoprint1 127.0.0.1:5001
        reqadd X-Scheme:\ https if needs_scheme { ssl_fc }
        reqadd X-Scheme:\ http if needs_scheme !{ ssl_fc }
        reqadd X-Script-Name:\ /taz2

#backend octoprint
#        acl needs_scheme req.hdr_cnt(X-Scheme) eq 0
#
#        reqrep ^([^\ :]*)\ /(.*) \1\ /\2
#        reqadd X-Scheme:\ https if needs_scheme { ssl_fc }
#        reqadd X-Scheme:\ http if needs_scheme !{ ssl_fc }
#        option forwardfor
#        server octoprint1 127.0.0.1:5000
#        errorfile 503 /etc/haproxy/errors/503-no-octoprint.http

backend webcam
        reqrep ^([^\ :]*)\ /webcam/(.*)     \1\ /\2
        server webcam1  127.0.0.1:8080
        errorfile 503 /etc/haproxy/errors/503-no-webcam.http
```

Now you need to start haproxy again and test things.

When connecting, make sure to use a trailing slash on the url, otherwise haproxy
gets mad

Now that both printers are accessible over the web interface, make final
configuration changes.

First, hit the wrench icon on the menu bar at the top to access the settings menu.

Type in `/dev/ttyLM*` or `/dev/ttyTAZ*` in the additional serial ports box,
depending on which pi you are configuring.

Select the appropriate serial port from the drop down box.

Now select printer profiles and add a new one.

Depending on which pi you are configuring, select either the Lulzbot Mini
options or the Lulzbot Taz 6 options below. Due to symlinks, these options will
only have to be configured once and will propagate to all octoprint instances on
ONE pi.

```config
Lulzbot Mini - default
General:
Name = lulzbot-mini
Identifier = (auto populated)
Model = Lulzbot Mini

Print bed & build volume:
Form Factor =  Rectangular
Origin = Lower Left
Heated bed = checked
Width (X) = 152mm
Depth (Y) = 152mm
Height (Z) = 158mm

Axes:
X = 4800mm/min (invert control unchecked)
Y = 4800mm/min (invert control unchecked)
Z = 1002mm/min (invert control unchecked)
E = 300mm/min

Hotend & extruder:
Nozzle Diameter: 0.5mm
Number of Extruders: 1

Lulzbot Taz 6 - default
General:
Name = lulzbot-taz-6
Identifier = (auto populated)
Model = Lulzbot Taz 6

Print bed & build volume:
Form Factor =  Rectangular
Origin = Lower Left
Heated bed = checked
Width (X) = 280mm
Depth (Y) = 280mm
Height (Z) = 250mm

Axes:
X = 6000mm/min (invert control unchecked)
Y = 6000mm/min (invert control unchecked)
Z = 200mm/min (invert control unchecked)
E = 300mm/min

Hotend & extruder:
Nozzle Diameter: 0.5mm
Number of Extruders: 1
```

Configure default temperatures under the temperatures tab. This will already be
encoded in the gcode file but its a nice little extra. This needs to be taken
care of per octoprint instance. If you feel confident in your terminal skills,
you can always copy paste this info from one `config.yaml` into another.

Give each instance a name under the appearance tab. I used template of B111 Lab
Mini/Taz #

Plugin Manager:

Disable CuraEngine and Discovery since we are not slicing on the Pis.

Hit restart.
