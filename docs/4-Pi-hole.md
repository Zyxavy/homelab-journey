# Installing Pi-hole in a Docker Container

Pi-hole will be deployed as a **Docker container**, keeping the system clean and making it easy to update, back up, or remove in the future.

---

## Directory Structure 

I am still refining how I organize my server files, but this structure works well for now and scales nicely as more services are added.

<img src="/assets/4-pihole/pihole-structure.JPG" alt="pihole" width="600" />

#### The Benefits of this are:
- Each service gets its own folder
- Configuration files are persisted using volumes
- I configured the `pihole/dnsmasq.d` directory to be in `docker/`, it's just a preference, you can configure yours to be in your pihole service directory.

---

## What is Pi-hole

Pi-hole acts as a network-wide ad blocker, so instead of installing ad blockers on every device in your network, Pi-hole filters ads and trackers at the DNS level. Though I found that some ads are still appearing, they are not as persistent as before and YouTube ads are not entirely blocked.

---

## Pi-hole Installation and Setup

- This is where Docker comes in, I chose to run Pi-hole in Docker to keep it isolated and make updates/removal easier if I break something.

1. Created a directory for pi-hole
```shell
    sudo mkdir -p ~/docker/home-server/pihole/config
    cd ~/home-server/pihole
```

2. Created the docker-compose.yml
```
   sudo nano docker-compose.yml
```

3. See the [docker/pi-docker-compose.yml](/docker/pi-docker-compose.yml) for my docker compose. 

4. Started pi-hole and verified that the container is running.
```shell
    docker compose up -d

    docker ps
```

5. Navigated to "http://[server-ip]:8081/admin" and entered the WEBPASSWORD

6. Some things I had to consider:
    - Ports may be blocked by ufw so allow them through: `sudo ufw allow 53/tcp`, `53/udp` and `sudo ufw allow 8081/tcp`
    - Password may not work in web interface, changed it by running: `docker exec pihole pihole -a -p PASSWORD` or leaving it blank `""`.

---

## How to use Pi-hole

After setting up Pi-hole, I log into my router's admin web UI, in my case it is **192.168.0.1**. In the DHCP settings, I changed the primary DNS server to the server's static IP address (**192.168.0.150**).


<img src="/assets/4-pihole/network-setup-1.png" alt="router web ui" width="600" />

<img src="/assets/4-pihole/network-setup-2.png" alt="router web ui" width="600" />



At this point, Pi-hole was working as expected. Even though it doesn’t block every ad, the overall browsing experience was better across all devices on my network.

---
## Adding Blocklist and Allow-list

In Pi-hole I can add more DNS blocklist to block more ranges of ads, I will be using [hagezi](https://github.com/hagezi/dns-blocklists?tab=readme-ov-file) block lists.



<img src="/assets/4-pihole/pi-hole.png" alt="pihole web ui" width="600" />

<img src="/assets/4-pihole/pi hole 2.png" alt="pihole web ui" width="600" />



After adding my desired block list I just need to update it by running `pihole -g` or through the web UI.


<img src="/assets/4-pihole/pihole update.png" alt="pihole web ui" width="600" />

That should be enough, I could add more, but more isn't necessarily better.

---

## Using Recursive DNS

To sum up, a recursive DNS starts from scratch and queries multiple DNS servers in a hierarchical fashion until it gets a final answer, rather than asking a single upstream server that already has the answer.

### Unbound

Unbound is an open-source recursive DNS resolver, this is what performs the recursive lookups.

- How it works is (my device) -> (Pi-hole) -> (Unbound) -> Internet DNS hierarchy

***The benefits of this are:***
- No need for third-party DNS provider like cloudflare and google.
- May be a little bit faster since after caching, responses comes directly from my server.

Though this might be slower for first-time searches.

## Installing and Using Unbound

1. I started by creating a directory for unbound in my `~/docker/home-server/unbound`. inside is a single `docker-compose.yml`.

2. For the docker-compose file see [docker-compose.yml](/docker/unbound-docker-compose.yml).

3. Then I run it `docker compose up -d` and modify my Pi-hole dns in the web UI to point to `127.0.0.1#53`.


<img src="/assets/4-pihole/pihole dns.png" alt="pihole dns ui" width="600" />


- For now I will be using `1.1.1.1` and `8.8.8.8` since they tend to be much faster for first time queries, but I will switch to unbound in the future.

I explored Unbound mainly for learning purposes, to better understand how DNS resolution works beyond using public resolvers.

---

# Summary

## What I learned
- Pi-hole is great for blocking ads in my network, though it requires a bit of setup, it's definitely worth it.
- Some ads may not get blocked so it is recommended to use an adblocker alongside Pi-hole.
- If my main concern is privacy(for now it isn't), I should be using Unbound. but due to my setup I will be using Cloudflare and Google DNS for now.
- If I need to block ads more aggressively, I can always add more list to my block lists.

## Why I did this
- Mainly to block ads on my phones and TV since I can't install an adblocker on those devices.
- For privacy (when I switch to using Unbound).

