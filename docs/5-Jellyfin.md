# The Jellyfin Media Server

Jellyfin is a free, open-source media server that will let me organize and stream my personal **totally legally** obtained collection of movies, shows, music, etc., to any devices connected to my network. There are others like Plex and Emby, but I prefer Jellyfin over them.

This is what made me build this home server. Similar to Pi-hole I plan to run this in a Docker container for easier management.

And as just like before, I will use this file structure:

<img src="/assets/5-jellyfin/early structure.JPG" alt="jellyfin structure" width="600" />

---

- The reason I separated the Jellyfin's media directory is purely my preference, I could place it inside `jellyfin/media`.
- I separated the genres accordingly so I can manage them easier inside the jellyfin web UI.

---

## Installing Jellyfin

Before installing I made sure to update and created a jellyfin and config directory in home-server `~/docker/home-server/jellyfin/config`.

1. Created a `docker-compose.yml` to run jellyfin, also made sure to set the media volume to `~/docker/media`.

2. See the [docker-compose.yml](/docker/jellyfin-docker-compose.yml) here.

3. Port 8096 is the default port for Jellyfin, I had no issues with that so I did not need to change it.

4. All I need to do is run it with `docker compose up -d` and I can finish the setup in the web UI.

5. ------------[WIP Web setup section]----------

---

## Transferring Files 

I needed a way to transfer my collection of movies and files to my server reliably. This is where the glorious `WinSCP` comes in.

- `WinSCP` is a free SFTP/FTP client for Windows. it lets me securely transfer and manage files between my computer and my server using various protocols.

It's a really convienient tool for managing my directories and setting things up.

As a test I transfered a test video from my computer to my server's `~/docker/media/shows` directory. In order for Jellyfin to read the video I had to create a directory and rename the video according to the format.

`Breaking Bad/Season 1/Breaking Bad S01 E01`, I had to use this format for easy access and for Jellyfin to correctly find the metadata for the show.

After scanning the library in Jellyfin, I can say that it works!

-------------[WIP Jelly web UI here]-------------

---

## Automation

I am already satisfied with this setup, but it can be better. I can automate finding the media, downloading, creating a directory and renaming them. Behold the `*arr stack`.

---

## What I learned
- Jellyfin is sufficient for my needs to stream to any of my devices.
- In order to avoid port conflict, I used Jellyfin's default port.
- `WinSCP` is the GOAT, though I have heard of `PuTTY` and I might try it.

## Why I did this
- This was my original goal, to be able to watch movies without constant buffering, and also because I don't have a Netflix subscription.
- Expose myself to managing, maintaining, and building a server that will be used by me.