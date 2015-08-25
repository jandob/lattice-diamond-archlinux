# Lattice Diamond on Archlinux

Download [Lattice Diamond 3.x](http://www.latticesemi.com/Products/DesignSoftwareAndIP/FPGAandLDS/LatticeDiamond.aspx)

Request a [new license file](http://www.latticesemi.com/Support/Licensing/DiamondAndiCEcube2SoftwareLicensing/DiamondFree.aspx)
from Lattice using the MAC address of your `eth0`

**NOTE** You MUST have an `eth0` otherwise you will see the message *"The hostid
of this system does not match the hostid specified in the license file"*.
If you do not have an eth0 device (probably because of
[this](http://www.freedesktop.org/wiki/Software/systemd/PredictableNetworkInterfaceNames/) ),
you can use the script `eth0DummyToggle` wich creates a dummy eth0 device.
(The MAC adress of the device can be configured inside the script).

Place `license.dat` and the downloaded `rpm` in the folder with the `PKGBUILD`,
then run

    $ makepkg
    $ sudo pacman -U lattice-diamond-*.pkg.*

start diamond with

    $ diamond

# machx02 breakout board
The driver for the onboard programmer is bundled with the diamond software,
but the linux kernel loads a different driver for the ftdi on the board. In
order to fix this add the following udev rule (e.g.
``/etc/udev/rules.d/98-lattice.rules`):

    ATTRS{idVendor}=="0403", ATTRS{idProduct}=="6010", MODE="0666"
    ATTRS{idVendor}=="0403", ATTRS{idProduct}=="6010", ATTRS{product}=="Lattice FTUSB Interface Cable", RUN+="/bin/bash -c '[[ $0 =~ .*\.0.* ]] && echo $0 > /sys/bus/usb/drivers/ftdi_sio/unbind' $kernel"
    
The first line gives the lattice diamond executable access to the usb device,
the second line disables the standard drivers for the first serial interface.
After the change reload udev:
    sudo udevadm control --reload
