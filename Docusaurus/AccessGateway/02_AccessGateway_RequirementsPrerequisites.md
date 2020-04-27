## AGW Requirements and Prerequisites

To setup a Magma Access Gateway (AGW), you will need a single-tenant physical **server** that satisfies the following recommended
requirements:

-   Any 64bit-X86 machine which can support a Debian Linux installation

-   Dual-core CPU, 2GHz or faster

-   2GB RAM

-   128GB-256GB SSD storage

-   2+ physical ethernet ports 
    We use enp1s0 and enp2s0 throughout this guide. They might have
    different names on your hardware, if so, replace enp1s0 and enp2s0
    with your current interface name in this guide. One port is for
    the SGi interface (default: enp1s0) and one for the S1 interface
    (default: enp2s0).

    -   Note: that the agw_install.sh script will rename the enp1s0 interface to eth0 and enp2s0 to eth1

-   A USB Flash Drive, to create a Debian boot disk

-   Debian [[iso
    Image]](http://cdimage.debian.org/mirror/cdimage/archive/9.9.0/amd64/iso-cd/debian-9.9.0-amd64-netinst.iso) for the boot disk
    ([[https://www.debian.org/CD/]](https://www.debian.org/CD/))

-   [[Etcher]](https://www.balena.io/etcher/)
    ([[https://www.balena.io/etcher/]](https://www.balena.io/etcher/))
    Use to create the boot disk with the Debian image using the [[tutorial here]](https://tutorials.ubuntu.com/tutorial/tutorial-create a-usb-stick-on-macos#0)
