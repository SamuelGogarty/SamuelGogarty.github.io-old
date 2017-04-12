
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
Again, __iwm0__ is a variable, in case you are using a different driver other then iwm.

If you see that the device has attached then you have successfully gotten your wireless card functional.

If you see a error that looks something like:

```markdown
SOIFCONFIG: Device not configured
```
Then you might have made a typo in /boot/loader.conf or you have the wrong driver loaded altogether.

Assuming you now are able to run the scan command mentioned earlier, take your ssid as shown in the can results and the password of the network and create a new file named wpa_supplicant.conf in /etc/.
The ultimate path of it should be /etc/wpa_supplicant.conf. Then put your network info inside this file, to do this follow:

First create/open the file with:

```markdown
ee /etc/wpa_supplicant.conf
```
How for your network info:

```markdown
network={
    ssid="your network name goes here"
    psk="your password goes here"
}
```
Now restart the network daemon in order to achieve net access:

```markdown
service netif restart
```
Now test your connection:

```markdown
ping 8.8.8.8
```

If you get packets pack from google then we can move onto the next step, if you don't the friendly people in #freebsd on the irc network irc.freenode.net, will gladly help.

Next quick step is just making sure that the pkg binary tree is working, to do this run:

```markdown
pkg update
```
As root. 
If you would like to use the ports tree instead run:

```markdown
portsnap fetch && portsnap extract
```

Next we will be getting the intel HD 5500 graphics card functional, this will be a far longer and more complicated step then what we just did, but not impossible.

Lets get started on getting the Intel HD 5500 graphics chip fully working.

Just like the wireless card the Intel HD 5500 graphics chip does not work out-of-the-box in the base system.

The driver for the intel HD 5500 graphics chip among others is called i915. Unlike iwm, you need to recompile the whole base system.

To do this, first you need to download the source tree of drm-next graphics stack.
You can find documentation for this [here](https://github.com/FreeBSDDesktop/freebsd-base-graphics/wiki).

But i'm going to put it here in order to keep everything in one place, of course.

```markdown
git clone https://github.com/FreeBSDDesktop/freebsd-base-graphics.git -b drm-next
```
now you need install llvm40 as a dependancy.

```markdown
pkg install llvm40
```

Or via ports:

```markdown
cd /usr/ports/devel/llvm40/ && make clean install
``` 

