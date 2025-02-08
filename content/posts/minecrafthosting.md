---
title: Hosting a Minecraft server with Docker
date: "2025-01-19"
categories:
  - minecraft
  - docker
  - tech
---

Hosting a Minecraft server is relatively easy compared to other services, but running it through Docker makes it so much more easy to use and portable. This is a complete guide on how to run a Minecraft server.

## Docker

First, we are going to install [Docker](https://www.docker.com/). _Do not_ install Docker desktop, as it's not really part of the docker open source community and we wouldn't want to deal with it. Here I assume you're using a Linux distribution, though the process should be similar for Windows users. Select your platform on [this](https://docs.docker.com/engine/install/) page. I use Ubuntu, so I'm going to follow the install guide for it.

After you have added Docker's apt repository and installed its packages, make sure to allow Docker to run as a non-root user for additional security and so that we don't have to enter the sudo password every time we want to interact with Minecraft. You can do that by following [this](https://docs.docker.com/engine/install/linux-postinstall/#manage-docker-as-a-non-root-user) guide in the Docker documentation. It is also generally a good idea to enable Docker to start on boot so that you don't have to start it yourself. Ubuntu and Debian already do this by default, but if you want to get the same behaviour on other distributions check [the docker documentation](https://docs.docker.com/engine/install/linux-postinstall/#configure-docker-to-start-on-boot-with-systemd).

Now we have Docker fully set up for our needs, time to move on to the next part.

## Networking

Because you probably want to be able to access your Minecraft server from outside your network, you should forward for the port 25565, which is the default port for Minecraft. You can skip this part if you host your Minecraft server on a network where all the ports are opened (commonly seen in VPSs and other servers in datacenters, rarely in home connections)

This step is hugely different for different ISPs and routers, so I can only give a general guide here. The process also depends on how your network is configured, for example for my Internet connection there is the ISP's firewall (the thing in which we open the ports) and then it passes through my D-Link router. I need to open the port 25565 on both of them for anything to work.

You can access your router's admin panel, in which you can open ports, by finding the router's IP, usually it is `192.168.0.1`. On Linux you can find it by typing `ip r` into a terminal. The IP will be displayed like this: `default via 192.168.0.1`. On Windows you can open cmd.exe and type in `ipconfig /all`. The router's IP will be displayed next to `Default Gateway`. Once you have it you can open a web browser, go to the router's IP and navigate to the login page. There you can enter the admin password. If you don't know what it is, you can usually find the default password online on websites such as [this](https://router-network.com/default-router-passwords-list) one.

Another thing you have to do to allow people to connect to your server is that you have to either give them your public IP address (can be found on a website like [whatismyipaddress.com](https://whatismyipaddress.com/)), or if you own a domain you can deploy an A record to the IP address you have Minecraft on. These might not work if you're behind a NAT, in which case you can use a tunnel like [playit.gg](https://playit.gg/).

For accessing Minecraft via a local network, you have to find your private IP address. On Linux you can do that by typing `ip a` into your terminal, and it will output the local IP address in the block that is _not_ `lo`, because that's the loopback address. And finally, you can access your Minecraft server locally (on the computer running it) by simply typing `localhost` into the server address field in the game.

## Starting the Server

I recommend you put each service you host into its own directory, such as `~/mc` for Minecraft, so that you can have a separate docker compose file for each service. Here is my docker-compose.yml:

```yaml
services:
  mc:
    image: itzg/minecraft-server
    environment:
      EULA: "true"
    ports:
      - "25565:25565" #forward this port on your router
    volumes:
      - ./data:/data
    stdin_open: true
    tty: true
    restart: unless-stopped
volumes:
  data: {}
```

To run it, you can simply use the command `docker compose up -d` (the -d flag makes it run as a background service) while inside the directory you put docker-compose.yml in. It will automatically download all the required files for thes server and start it. To check the logs you can run `docker compose logs -f`.

## Server Administration

You might need to log into the admin console sometimes, for example if you'd like to whitelist someone buy you don't want to start Minecraft. What you can do is use rcon-cli, a tool that comes with the minecraft docker container that allows you to do just that - log into the server console. To run it, go to the folder you have the docker compose file in and use the command `docker compose exec -i mc rcon-cli` (mc is the service's name).

Minecraft's `server.properties` file can be found in the data folder. In the file you can configure the server, such as the resource packs, the game mode or the OP privilege level.

I highly recommend upgrading the docker container from time to time to get the latest security patches and Minecraft versions. You can upgrade the host machine (Ubuntu in my case) by running `sudo apt update && sudo apt dist-upgrade` or your distribution's equivalent. As for Minecraft you can go to the directory you have it in and run `docker compose pull && docker compose up -d`

## Final Thoughts

Overall, I think docker and docker compose make running and admining a Minecraft server a lot easier, and it allows you to very easily move your server from one device to another without having to deal with the OS running on it.
