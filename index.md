
Here is my first blog showing my personal experience of getting FreeBSD working on a my Thinkpad T450s in hopes that it will serve someone else.

Before we get started on the actual installation and configuration process, some useful notes to consider that can differ on your personal machine;

My hardware inventory on this T450s, consists of:

* Processor: Intel(R) i5-5300U @ 2.3 GHz with 2 cores and 4 threads.
* Memory: 12 GB DDR4, 4 GB soldered, 8 GB soketed.
* Graphics: Intel(R) HD 5500 (Broadwell GT2)
  * Driver(s): i915kms, drm. see [here](https://wiki.freebsd.org/Intel_GPU).
* Storage: 128 GB HITACHI(R) SSD.
* Display: 1600x900 TN built in Thinkpad display and a 3648x1152 Samsung(R) VGA monitor.
* Wireless: Intel Dual Band Wireless AC 7265
  * Driver(s): if_iwm, iwm7265fw, iwm7265Dfw. see [here](https://www.freebsd.org/cgi/man.cgi?query=iwm&sektion=4).
* Battery: 1 default 86+ lenovo battery, 3 cell 54 Wh and 1 internal 3 cell 54 Wh

This page assumes you have the base system of FreeBSD 11.0-RELEASE installed on your machine, if you do not see [here](https://www.freebsd.org/doc/handbook/bsdinstall-pre.html).

I will also be assuming you are using the same model of thinkpad as me, a thinkpad T450s if you havent caught on.
similar models may have a very similar process if not the same.

This guide will be taking care of these major things:
* Getting the iwm wireless driver working.
* Getting the intel DRM graphics stack working.
* Installing and Configuring Xorg.
* Configuring audio.
* Configuring synaptics trackpad driver.
* Fine tuning of the FreeBSD kernel.

Since you will need a internet connection to take care of any task obviously the first step is getting WI-FI to work.
This step can vary wildly in success, my T450s came with a special snowflake of a realtek chip that isn't even supported by the FreeBSD [rtwn](https://www.freebsd.org/cgi/man.cgi?query=rtwn&sektion=4&manpath=freebsd-release-ports) driver.
Luckily however i had a T450 lying around with a intel wireless card. Specifically the one listed in the hardware inventory, which I was able to get working pretty easily.

In order to get this chip working you need the iwm driver either compiled into your kernel or loaded as a kernel module. It is **not** included by default.

The iwm driver supports the following wireless cards:
* Intel Dual Band Wireless AC 3160
* Intel Dual Band Wireless AC 3165
* Intel Dual Band Wireless AC 7260
* Intel Dual Band Wireless AC 7265
* Intel Dual Band Wireless AC 8260

This is easily accessible documentation about this driver and how to get it working [here](https://www.freebsd.org/cgi/man.cgi?query=iwm&sektion=4), but I wanted to keep everything in one place so i listed it here as well.

Since we are going to compile the kernel twice anyway lets just take the easy route and just load it as a module.
To do this let's open /boot/loader.conf with a text editor, the default preinstalled editor is [ee](https://www.freebsd.org/cgi/man.cgi?query=ee&sektion=1).

Add the following lines:

```markdown
if_iwm_load="YES"
```
Depending on which wireless card you specifically have, you will also need to add *1* of the following depending on your model of wireless card:

```markdown
iwm3160fw_load="YES"
iwm7260fw_load="YES"
iwm7265fw_load="YES"
iwm8000Cfw_load="YES"
```
If you are unsure and too lazy to check simply add all of them, no harm in it besides being less _minimalistic_
For my chip it was the 7265 firmware.

Adding the driver alone will not get you a active internet connection. You must also get the driver to use DHCP in order to get you a ip address.
Add the following to /etc/rc.conf:

```markdown
ifconfig_iwm0="DHCP"
```
_iwm0_ can change depending on what wireless card you are using. for example if you were using the rtwn driver instead, it would be:

```markdown
ifconfig_rtwn0="DHCP"
```
Okay now reboot, you will notice you still cannot ping google. Obviously you need to add a network first.
First make sure your **wlan0** interface is even loaded. run:
```markdown
ifconfig | grep wlan
```
If you see something similar to:

```markdown
wlan0: flags=8843<UP,BROADCAST,RUNNING,SIMPLEX,MULTICAST> metric 0 mtu 1500
```
Then all is fine, if you **don't** see that you can attempt to attach the device with:

```markdown
ifconfig wlan create wlandev iwm0
```

To do this you can scan for networks with:

```markdown
ifconfig wlan0 up scan
```


