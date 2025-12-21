# Early Version of the Structure

Before writing this, I had already established my initial directory layout. This structure is subject to change as my server evolves, but it provides a solid starting point.

<img src="/assets/3-structure-docker/early structure.JPG" alt="Current" width="600" />

---

I am still experimenting with the structuring of my project but for now I am satisfied with this. `docker/` will house the media folder as well as the services that are being used. Since I plan on using `jellyfin` and `nextcloud`, having a separate `media/` directory would be nice. `home-server/` will contain individual service folders and their respective `docker-compose.yml` and `configs/`.

My main goal was making a working structure rather than perfecting it in the first try, I wanted a layout that would make sense to me now, even if I end up refactoring later.

---

## Docker installation

- Using Docker keeps my services isolated and avoid cluttering the base system as I experiment. This is also great for configuring my *arr stack later on.

1. I had already installed the required dependencies so I just need to download the packages from the Docker's repository

```bash
    #Ensures the packages are trusted
    sudo install -m 0755 -d /etc/apt/keyrings

    #Download and import Docker's official GPG key
    curl -fsSL https://download.docker.com/linux/debian/gpg | \
    sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

    #Set appropriate read permissions for the key
    sudo chmod a+r /etc/apt/keyrings/docker.gpg
```

2. Then I add the repository to the system's package sources
```bash
    echo \
    "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
    https://download.docker.com/linux/debian \
    $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
    sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

3. Update the package and install Docker
```bash
    sudo apt update -y

    sudo apt install -y docker-ce docker-ce-cli containerd.io \
    docker-buildx-plugin docker-compose-plugin
```

4. After that, I verified the status of Docker
```bash
    sudo systemctl status docker

    sudo docker --version
    sudo docker compose version
```

5. Ran this command so that I can run docker without sudo for convenience
```bash
    sudo usermod -aG docker zyxary #my user is zyxary
```

6. Exited and logged back in for the command to take effect.

7. Test Docker to confirm it works
```bash
    docker run hello-world
```
---

## What I learned:

- Obsessing over creating a perfect structure is a waste of time, sooner or later I will need to refactor it again.
- Docker is great for containing my services creating a barrier to protect my base system.

