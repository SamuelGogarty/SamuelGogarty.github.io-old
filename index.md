###FreeBSD on a modern Thinkpad

Here is my first blog showing my personal experience of getting FreeBSD working on a my Thinkpad T450s in hopes that it will serve someone else.

Before we get started on the actual installation and configuration process, some useful notes to consider that can differ on your personal machine;

##Hardware

My hardware inventory on this T450s, consists of:
markdown
	Processor: Intel(R) i5-5300U @ 2.3 GHz with 2 cores and 4 threads.
	Memory: 12 GB DDR4, 4 GB soldered, 8 GB soketed. no company given.
	Graphics: Intel(R) HD 5500 (Broadwell GT2) Driver(s): i915kms, drm.
	Storage: 128 GB HITACHI(R) SSD
	Display: 1600x900 TN built in Thinkpad display and a 3648x1152 Samsung(R) VGA monitor
	Wireless: Intel Dual Band Wireless AC 7265 Driver(s): if_iwm, iwm7265fw, iwm7265Dfw.
```markdown
- Processor: Intel(R) i5-5300U @ 2.3 GHz with 2 cores and 4 threads.
- Memory: 12 GB DDR4, 4 GB soldered, 8 GB soketed. no company given.
- Graphics: Intel(R) HD 5500 (Broadwell GT2) Driver(s): i915kms, drm.
- Storage: 128 GB HITACHI(R) SSD.
- Display: 1600x900 TN built in Thinkpad display and a 3648x1152 Samsung(R) VGA monitor.
- Wireless: Intel Dual Band Wireless AC 7265 Driver(s): if_iwm, iwm7265fw, iwm7265Dfw.
```


##For more info

For more information regarding the mentioned device drivers:
	i915kms, drm: [https://wiki.freebsd.org/Intel_GPU](https://wiki.freebsd.org/Intel_GPU)
	if_iwm, iwm7265fw, iwm7265Dfw: [https://www.freebsd.org/cgi/man.cgi?query=iwm&sektion=4](https://www.freebsd.org/cgi/man.cgi?query=iwm&sektion=4)
	

This page assumes you have the base system of FreeBSD 11.0-RELEASE installed on your machine.
If you do not see [https://www.freebsd.org/doc/handbook/bsdinstall-pre.html](https://www.freebsd.org/doc/handbook/bsdinstall-pre.html)

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

