> Fork of [macbook-lighter](https://github.com/harttle/macbook-lighter)

# macbook-lighter-nvidia

MacBook keyboard and screen backlight adjust on the ambient light.
Internally, macbook-lighter reads the following files:

* /sys/devices/platform/applesmc.768/light
* /sys/class/backlight/nv_backlight/brightness
* /sys/class/backlight/nv_backlight/max_brightness
* /sys/class/leds/smc::kbd_backlight/brightness
* /sys/class/leds/smc::kbd_backlight/max_brightness

So you're expected to install corresponding Nvidia/Intel drivers first.

## Setup

All commands including macbook-lighter-kbd, macbook-lighter-screen
will be available with sudo privilege once macbook-lighter-nvidia finished install.

To use in non-root environment such as [xbindkeys](https://wiki.archlinux.org/index.php/Xbindkeys),
it's recommended to setup an "udev" rule to allow users in the
"video" group to set the backlights.
Place a file /etc/udev/rules.d/90-backlight.rules containing:

```
SUBSYSTEM=="backlight", ACTION=="add", \
  RUN+="/bin/chgrp video /sys/class/backlight/%k/brightness", \
  RUN+="/bin/chmod g+w /sys/class/backlight/%k/brightness"
```

And a file /etc/udev/rules.d/91-leds.rules containing:

```
SUBSYSTEM=="leds", ACTION=="add", \
  RUN+="/bin/chgrp video /sys/class/leds/%k/brightness", \
  RUN+="/bin/chmod g+w /sys/class/leds/%k/brightness"
```

Or Create/Add these commands to `/etc/rc.local` file to change the backlight file mode bits:
```
# /etc/rc.local
.
.
.
chmod 777 /sys/class/leds/smc::kbd_backlight/brightness
chmod 777 /sys/class/backlight/nv_backlight/brightness
.
.
. exit 0
```

> Note: If you dont have /etc/rc.local file and rc-local.service (Check by executing `sudo systemctl status rc-local`), follow these steps to create one or both files:

1. Create `/etc/rc.local` file by running `sudo vim /etc/rc.local` and paste these:
```
# /etc/rc.local

#!/bin/sh -e
#
# rc.local
#
# This script is executed at the end of each multiuser runlevel.
# Make sure that the script will "exit 0" on success or any other
# value on error.
#
# In order to enable or disable this script just change the execution
# bits.
#
# By default this script does nothing.

# <Add terminal commands here without sudo>
chmod 777 /sys/class/leds/smc::kbd_backlight/brightness
chmod 777 /sys/class/backlight/nv_backlight/brightness

exit 0
```

2. Make the file executable using `sudo chmod +x /etc/rc.local`

3. If you don't have `rc-local` service too, then:

    a.  Create one by executing `sudo vim /etc/systemd/system/rc-local.service` and paste these

     ```
        # /etc/systemd/system/rc-local.service

        [Unit]
        Description=/etc/rc.local Compatibility
        ConditionPathExists=/etc/rc.local

        [Service]
        Type=forking
        ExecStart=/etc/rc.local start
        TimeoutSec=0
        StandardOutput=tty
        RemainAfterExit=yes
        SysVStartPriority=99

        [Install]
        WantedBy=multi-user.target
  
     ```
     b.  Enable it to start with system by running `sudo systemctl enable rc-local` and reboot. Check the status of your service file by executing `sudo systemctl status rc-local`.


## Usage

```bash
# Increase keyboard backlight by 50
macbook-lighter-kbd --inc 50
# Increase screen backlight by 50
macbook-lighter-screen --inc 50
# Set screen backlight to max
macbook-lighter-screen --max
# start auto adjust daemon
systemctl start macbook-lighter
# start auto adjust interactively, root previlege needed
macbook-lighter-ambient
```

## Tested MacBook Versions

* MacBook Pro Mid 2010 13" (7,1)
