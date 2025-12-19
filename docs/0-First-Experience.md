# Building a Low-End Home Server
---

I started my journey around 6 months ago, I would **Legally** Download  Movies, Shows and Animes from trusted sources into my USB flashdrive then plug that into the TV to watch from there. When I realized an old PC could eliminate the USB shuffle, I stumbled into the world of homelabbing.

---

### The "Server"
- **CPU:** 3rd-gen Intel i3  
- **RAM:** 4GB DDR3 (originally)  
- **Storage:** 500GB HDD (initially)  
- Slow, but sufficient for my needs.

### Attempt 1: Ubuntu Server
I first tried Ubuntu server on it, it was slow and I had a hard time installing and trouble-shooting. it would randomly turn off at times and would be unstable if left running for a while. turns out the hdd and the ram were failing. so I swapped the 500gb hdd for a 1tb hdd I had lying around. then swapped the 4gb of RAM for another 4gb ddr3 RAM. it works but ubuntu was a bit unstable and consumed 700mb of memory at idle.

### Attempt 2: Debian 12
A fresh install of Debian 12 went smoothly. With only SSH and system utilities, it idled at ~250MB RAM. I had it running for a few months until I realized that the I was using the root partition for not only my services but also for my Movies, Music, Backup files and photos.

### The Bind-Mount Blunder
Rather than repartition properly with Gparted (which felt like too much hassle), I tried to cleverly `bind` mount system directories (/var, /opt, /docker, etc.) into my /home directory via SSH. and boy was it a teribble mistake. it corrupted my drive the next day and I had to start over again. 

<img src="/assets/0-first-experience/1.jpg" alt="Errors" width="600" />



<img src="/assets/0-first-experience/2.jpg" alt="Errors" width="600" />

---

**And that brings us here.**  
This is my documented rebuild on a cleaner attempt at building a stable home server from the ground up.