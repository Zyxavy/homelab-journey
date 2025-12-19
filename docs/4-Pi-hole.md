# Installing Pi-hole in a Docker Container

Pi-hole will be deployed as a **Docker container**, keeping the system clean and making it easy to update, back up, or remove in the future.

---

## Directory Structure 

I am still refining how I organize my server files, but this structure works well for now and scales nicely as more services are added.

<img src="/assets/4-pihole/pihole-structure.JPG" alt="pihole" width="600" />

- Each service gets its own folder
- Configuration files are persisted using volumes
- I configured the `pihole/dnsmasq.d` directory to be in `docker/`, it's just a preference, you can configure yours to be in your pihole service directory.

---

## What is Pi-hole

Pi-hole acts as a network-wide adblocker, so instead of installing ad blockers on every device in your network, Pi-hole filters ads and trackers at the DNS level. Though I found that some ads are still apearing they are not as persistent as before and YouTube ads are not entirely blocked.

---

## Pi-hole Installation and Setup

1. Create a directory for pi-hole
```shell
    sudo mkdir -p ~/docker/home-server/pihole/config
    cd ~/home-server/pihole
```

2. Create the docker-compose.yml
```
   sudo nano docker-compose.yml
```

3. See the [docker/pi-docker-compose.yml](/docker/pi-docker-compose.yml) for reference. 

4. Start pi-hole and verify that the container is running.
```shell
    docker compose up -d

    docker ps
```

5. Open a browser and navigate to "http://[server-ip]:8081/admin" and enter your WEBPASSWORD

6. Some things to consider:
    - Ports may be blocked by ufw so allow them via `sudo ufw allow 53/tcp`, `53/udp` and `sudo ufw allow 8081/tcp`
    - Password may not work in web interface, change it by running `docker exec pihole pihole -a -p PASSWORD` or leave it blank `""`.

---

## How to use Pi-hole

After setting up Pi-hole, log into your router's admin web ui, in my case it is **192.168.0.1**. Find the dhcp settings and change the main DNS to the server's static IP address, for me it is **192.168.0.150**.

<img src="/assets/4-pihole/network-setup-1.png" alt="router web ui" width="600" />

<img src="/assets/4-pihole/network-setup-2.png" alt="router web ui" width="600" />