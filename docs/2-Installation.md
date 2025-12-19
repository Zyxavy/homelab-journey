# Installing Debian
---

## Preparation

Before starting the installation, make sure you have the following:

- A spare USB flash drive
- The **Debian 13 ISO image**, which you can download from the official website:   [https://www.debian.org/distrib/](https://www.debian.org/distrib/)
- A flashing tool such as **Rufus** or **Balena Etcher** to create a bootable USB.

---

## Installation Process

Once downloaded, flash the Debian ISO to your USB drive using your preferred tool.


1. After that you can continue with the installation, power your PC on and repeatedly press the **BIOS/UEFI key** to access the boot menu, in my case, the key was `F11`, which allowed me to select the boot device.

2. Choose the USB flash drive as the boot source.

3. Proceed with the installation. The **Graphical Install** relatively easy to understand. 

4. During disk partitioning, I chose to use **LVM (Logical Volume Manager)** so that the partitions can be resized later using tools like **GParted**.

My partition layout:
- `130 GB` for `/` (root)
- `4 GB` for `swap`
- Remaining space for `/home`

5. It will ask for what to install. in my case I dont need a desktop environment so I went ahead and just installed an SSH server and standard system utilities.

---

## Post-installation.

1. Restart your PC and remove the usb flashdrive. 
2. You can also revert the settings back in the BIOS/UEFI if you want.

- In my fresh install I had no `sudo` installed yet so my user 'zyxary' did not have `sudo` privileges. To fix this, I temporarily switched to the `root` user. (**Note:** It is not recommended to operate as `root` all the time.)

```bash
    # Switch to root
    su

    #or
    su -
```

- Enter your root password then update and install sudo

```bash
    #Update and upgrade the package list
    apt update && apt upgrade -y

    #Install sudo
    apt install sudo -y

    usermod -aG sudo zyxary #This add the user to the sudo group
```

- Now you can exit and log back in to your user
```bash
    exit
```

### Install various tools and Dependency

 I plan to run most services in Docker for isolation and easier management. Before installing Docker itself, I need to install essential utilities and security tools:

```bash
    sudo apt install ca-certificates curl gnupg lsb-release git ufw fail2ban htop ncdu net-tools iproute2 unzip zip
```
---

### Configuring a Static IP Address

By default, most Debian installations obtain an IP address via **DHCP**, which means the IP can change over time.  
For a server a **static IP address** is recommended because it:

- Prevents IP changes after reboots
- Ensures services remain reachable
- Required for DNS servers (Pi-hole)

This configuration is done entirely on the server side, without relying on router-based DHCP reservations.

1. First, determine the name of your active network interface. For me it is wlan0.

```bash
    ip a
```

2. You will need the following information:
    - Static IP: 192.168.0.150 <-- My desired IP Address for my server
    - Gateway: 192.168.0.1 <-- usually your router
    - Subnet mask: 255.255.255.0
    - DNS servers:
        - 1.1.1.1
        - 8.8.8.8

3. Edit the network configuration through nano.
```bash
    sudo nano /etc/network/interfaces.d/wlan0
```

4. Add the configuration below. **NOTE:** Make sure that the interface name, IP address and gateway is correct.

```bash
    auto wlan0
    iface wlan0 inet static
    address 192.168.0.150
    netmask 255.255.255.0
    gateway 192.168.0.1
    dns-nameservers 1.1.1.1 8.8.8.8

```

5. Restart the network to apply the changes
```bash
    sudo systemctl restart networking

    #if an error occurs, restart the interface
    sudo ifdown wlan0 && sudo ifup wlan0
```

6. Verify the static IP and that you are connected to the internet.

```bash
    #You should see inet 192.168.0.150/24 or your desired IP address
    ip a show wlan0

    #Test connection
    ping -c 3 192.168.0.1
    ping -c 3 google.com

```

7. If it fails you can revert back to DHCP, and add a reservation through your router's admin UI.
```
# Edit the config file again and change 'static' back to 'dhcp'
```


---

- You can follow the official Debian installation guide here: [Debian 13 (Trixie) Installation Manual](https://www.debian.org/releases/trixie/amd64/) 

- Or if you prefer a visual walkthrough, this YouTube tutorial covers the entire process: [Debian 13 Full Installation guide](https://www.youtube.com/watch?v=RjdMbYhjbCs)

---

| Package                | Purpose                                                                 |
|------------------------|-------------------------------------------------------------------------|
| **ca-certificates**    | SSL certificates for secure HTTPS connections                           |
| **curl & gnupg**       | Required to download and verify Docker's repository key                 |
| **lsb-release**        | Helps identify your Debian version for repository setup                 |
| **git**                | Version control (for cloning configs and projects)                      |
| **ufw**                | Uncomplicated Firewall (for basic network security)                     |
| **fail2ban**           | Blocks brute-force SSH attacks                                          |
| **htop**               | Interactive process viewer                                              |
| **ncdu**               | Disk usage analyzer                                                     |
| **net-tools & iproute2**| Network troubleshooting utilities (`ifconfig`, `ip`, etc.)            |
| **unzip & zip**        | Archive handling                                                        |