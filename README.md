# Arr, matey!

## About

This repository serves as personal documentation for running the *arr stack on a Synology NAS. Please note that this is not a comprehensive guide, but rather a reminder for myself.

This *arr stack includes the following components:

- 📺 [Sonarr](https://sonarr.tv/): A TV series management and automation tool.
- 🎬 [Radarr](https://radarr.video/): A movie management and automation tool.
- 🔎 [Prowlarr](https://prowlarr.com/): An indexer for Usenet and Torrent files.
- 📥 [SABnzbd](https://sabnzbd.org/): A Usenet client.
- 🔄 [Transmission](https://transmissionbt.com/): A lightweight BitTorrent client.

## Shares

### Folder Structure

To set up the required folder structure, create two new shares: `arr` and `docker`, along with their sub-folders as shown below:

```
arr
├── media
│   ├── movies
│   └── series
├── torrents
│   ├── complete
│   └── incomplete
└── usenet
    ├── complete
    └── incomplete
docker
├── portainer
├── prowlarr
├── radarr
├── sabnzbd
├── sonarr
└── transmission
```

The `arr` share will hold all media downloads, while the `docker` share will hold the configurations for the running Docker containers.

It's important to store media downloads and organized media within the same share. This enables Sonarr and Radarr to use hardlinks when moving downloaded media (`arr/{torrents,usenet}/complete`) into the organized media (`arr/media`).

### Permissions

To set the correct permissions for the shares, follow these steps:

1. Create a new user named `docker` with read and write access to the `arr` and `docker` shares.
2. Use the terminal to set the permissions for both shares:

```sh
$ sudo chown -R docker:users /volume2/arr /volume2/docker

$ sudo chmod -R a=,a+rX,u+w,g+w /volume2/arr /volume2/docker
```

## Docker

### Package Install

Install the Docker package from the NAS's Package Center.

### Portainer

Instead of manually creating the Docker containers using the Docker package UI, we can automate the process using Portainer.

To run Portainer as a container on the Synology NAS, follow the instructions in the [Install Portainer CE with Docker on Linux](https://docs.portainer.io/start/install-ce/server/docker/linux) guide.

```sh
$ sudo docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v /volume2/docker/portainer:/data portainer/portainer-ce:latest
```

Access Portainer at https://nas.home.arpa:9443/ and go through the setup wizard. Add a new stack that points to this GitHub repository. Portainer will use the `docker-compose.yml` file to create the *arr Docker containers.

You can load the environment variables from the included `.env` file. Make sure the `PUID` and `PGID` variables are set correctly. To find the user ID (PUID) and group ID (PGID) for the `docker` user, run the following command:

```sh
$ id docker
uid=1034(docker) gid=100(users) groups=100(users)
```
