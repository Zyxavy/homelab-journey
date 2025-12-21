# Installing Debian
---

## Preparation

Before starting the installation, I took note of the following:

- A spare USB flash drive
- The **Debian 13 ISO image**, which you can download from the official website:   [https://www.debian.org/distrib/](https://www.debian.org/distrib/)
- A flashing tool, I used Rufus back when I was installing, now I use ventoy to store multiple ISO files.

---

## Installation Process

1. I installed the OS as usual but with a couple of tweaks for my use case. 

2. During disk partitioning, I chose to use **LVM (Logical Volume Manager)** so that the partitions can be resized later using tools like **GParted**.

My partition layout:
- `130 GB` for `/` (root)
- `4 GB` for `swap`
- Remaining space for `/home`

3. in my case, I didn't need a desktop environment so I went ahead and just installed an SSH server and standard system utilities.

---

## Post-installation.

1. In my fresh install I had no `sudo` installed yet so my user 'zyxary' did not have `sudo` privileges. To fix this, I temporarily switched to the `root` user. (**Note:** It is not recommended to operate as `root` all the time.)

```bash
    # Switch to root
    su

    #or
    su -
```
2. It is usually recommended to update your OS first before doing anything else, so I did just that and installed `sudo` right after.

```bash
    #Update and upgrade the package list
    apt update && apt upgrade -y

    #Install sudo
    apt install sudo -y

    usermod -aG sudo zyxary #This adds the user to the sudo group
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

This configuration is done entirely on the server side, without relying on router-based DHCP reservations. Doing this over Wi-Fi was slightly risky, but since the server isn’t in an ideal location yet, it was the most practical option.

1. First, I need to identify my network interface, which is wlan0.

```bash
    ip a
```

2. I used the following configuration.
    - Static IP: 192.168.0.150 <-- My desired IP Address for my server
    - Gateway: 192.168.0.1 <-- my router's gateway
    - Subnet mask: 255.255.255.0
    - DNS servers:
        - 1.1.1.1
        - 8.8.8.8

3. Then I edit the network configuration through nano.
```bash
    sudo nano /etc/network/interfaces.d/wlan0
```

4. Add the configuration below.

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

6. Verify the static IP and checking connection to the internet.

```bash
    #The output was 192.168.0.150/24
    ip a show wlan0

    #Test connection
    ping -c 3 192.168.0.1
    ping -c 3 google.com

```

7. Thankfully it worked to I had no need to revert back to DHCP, and add a reservation through the router's admin UI.

---

- I recommend following the official Debian installation guide here: [Debian 13 (Trixie) Installation Manual](https://www.debian.org/releases/trixie/amd64/) 

- Or through this YouTube tutorial: [Debian 13 Full Installation guide](https://www.youtube.com/watch?v=RjdMbYhjbCs)

---

## What I learned:

- LVM ensures that I don't make the same mistake of mount bindng again. It's a much safer option than relying on bind mounts or fixed partitions.
- A minimal install of SSH + system utilities only, keeps resource usage low and avoids unnecessary garbage on my server.
- Wi-Fi works for now, but it has risk during network changes. Switching to Ethernet is a must once my physical setup allows it.

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