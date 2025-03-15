---
title: "[Vulnlab] Build your own Vulnlab with Docker"
date: 2025-03-13 12:00:00 -500 
categories: [Vulnlab, Installation]
tags: [soc,redteam,sqli,docker,vulnlab]
author: Christoph K
---

# Vulnlab Installation

## Installation Docker + Portainer

Welcome! After a short break I created my own open source `Vulnlab`. It is hosted on a `Docker` Container on a `Raspberry Pi 5`. 

With `Docker` I can get very fast an Application which I can host in my network for penetration testing. 


Below a very short Installation Guide for installing `Docker` on a Linux Device (in my case a Raspberry Pi) with `Portainer`:



1.  We download the Docker script from Docker 
`curl -fsSL https://get.Docker.com -o get-Docker.sh`
2. Execute the script
`sudo sh get-Docker.sh`
3. Add the current user to the group `docker`:
`sudo usermod -aG docker $USER`
4. This applies the new group membership without requiring a full logout.
`newgrp docker`
5. This pulls and runs the official hello-world Docker image: `docker run hello-world`

6. Creates a persistent volume named portainer_data.
Volumes are used to store persistent data outside of containers.
`docker volume create portainer_data`

7. This runs and gets the container `Portainer` on Port `9443`.

```bash
docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest
```

8. Login on `127.0.0.1:9443`. 


It should look like that:

![portainer](assets/img/vulnlab/intro/01.png)


## Installation Vulnlab from Yavuzlar


Now that we have docker running, we can easily install and manage our apps with it and `portainer` in a GUI. 

[Github Yavuzlar/VulnLab](https://github.com/Yavuzlar/VulnLab)


For the installation we do the following:

Manual Installation
1. Clone the repo `git clone https://github.com/Yavuzlar/VulnLab`
2. Build docker image `docker build -t yavuzlar/vulnlab .`
3. Run container `docker run -d -p 1337:80 yavuzlar/vulnlab`


Now we should see the container in `Portainer` and reach it on port `1337`:

![portainer2](assets/img/vulnlab/intro/02.png)


The application looks like that:

![vulnlab](assets/img/vulnlab/intro/03.png)



Hope you found this guide useful. Now we can try all of our red team techniques on our own hosted app. 

