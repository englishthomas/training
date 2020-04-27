## Access Gateway Setup (Bare Metal Setup) <br><br/>

Complete the Magma AGW prerequisites prior to proceeding.<br><br/>

## Create a Boot Disk

Follow the below steps to create a USB boot stick and install Debian GNU/Linux on your AGW host onto a single-tenant physical server.

    1.  Download an .iso image from [[Debian
        mirror]{.underline}](http://cdimage.debian.org/mirror/cdimage/archive/9.9.0/amd64/iso-cd/debian-9.9.0-amd64-netinst.iso)

    2.  Create a bootable USB using Etcher [[tutorial
        here]{.underline}](https://tutorials.ubuntu.com/tutorial/tutorial-create-a-usb-stick-on-macos#0)

    3.  Boot your AGW host from USB\
        
        (Press F11 to select boot sequence, caution, this may differ from your machine). Select the non-UEFI option if prompted

    -   Select "Install" option

    -   Network missing firmware "No"

    -   Primary network interface \"enp1s0\"

    -   Configuration
            Hostname: "magma"
            Domain name: ""

    c.  Root password: "magma"

    d.  Username: "magma"

    e.  Password: "magma"

    f.  Partition disk: \"Use entire disk\"

    g.  Select disk to partition: \"sda\"

    h.  Partitioning scheme: \"All files in one partition\"

    i.  Software selection: Only tick 'ssh server' and 'utilities'(untick all others)
    

    4.  Connect your SGi interface to the Internet and select this port during the installation process to get an IP using DHCP.<br><br/>

## Deploy Magma on the AGW host

Run the following commands:

```
    su
    wget
    https://raw.githubusercontent.com/facebookincubator/magma/master/lte/gateway/deploy/agw_install.sh
    sh agw\_install.sh
```

The machine will reboot but it\'s not finished yet, the script is still running in the background. You can follow the output by running:

```
journalctl -fu agw\_installation
```

When you see AGW installation is done it means that your AGW installation is complete.

Verify Magma is running by executing:

```
service magma@* status
```

