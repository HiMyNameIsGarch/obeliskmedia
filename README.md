# Obelisk Media
I have created this repository to document my process of making a home media
server using an old laptop via Jellyfin. The goal of this project is to create my own
hosted server.

# Table of Contents
- [Before installation requirements](#before-installation-requirements)
- [Installation process](#installation-process)
- [Post installation requirements](#post-installation-requirements)
- [CasaOS](#casaos)
- [Server automation](#server-automation)
- [Security considerations](#security-considerations)
- [Troubleshooting](#troubleshooting)

# Before installation requirements
- To make this work you will need at least 4GB of RAM and a 64-bit CPU ( at
least 2 cores and 4 threads ).
- When it comes to storage, you will need at least 100GB of free space for the
operating system and applications. For media storage, it depends on your needs.
- The interesting part is choosing an operating system. I recommend using a
lightweight Linux distribution such as Ubuntu Server or Debian depending on
your hardware, skills and preferences.

# Installation process
- Download the ISO file, use dd to write it to a USB drive and have fun at installing it.
- I recommend using a minimal installation to save resources and only install the
necessary packages. I choose Ubuntu Server LTS for this project. As my setup is as follows:
    - CPU: Intel i5-430m
    - RAM: 4GB DDR3 ( may upgrade in the future )
    - Storage: 300GB HDD ( may upgrade in the future )

# Post installation requirements
Install the latest updates for your operating system. Let's configure the
ssh once in for all. Firstly run `sudo apt install openssh-server`. It should be
up and running. Then go into some machine, generate a new ssh key and copy it with
`ssh-copy-id` onto the remote host( aka the server ). Don't close the session, because
we are going to apply some changes to it:

The following changes are kinda mandatory for a "so to speak" home made server,
the changes are made in `/etc/ssh/sshd_config`, use you favourite editor (vi):

- `Port 6234`
Here choose a random port
- `MaxAuthTries 2`
- `PermitRootLogin no`
- `PasswordAuthentication no`
- `PermitEmptyPasswords no`
Perfection, give a `sudo systemctl restart ssh` and we are good to go.

# CasaOS
For this setup I will use CasaOS with Docker. By default I don't think it will
be installed out of the box via Ubuntu installation wizard. My advice is to
follow their [official guide](https://docs.docker.com/engine/install/ubuntu/).
Don't use `ufw` use directly `iptables` as docker does not like for some reason
`ufw`.

Perfection. Now it's time to shine.

Grab a coffee or a cup of tea and run the following command (it's from their
official website btw):
```
curl -fsSL https://get.casaos.io | sudo bash
```
Now from their UI, you can install Jellyfin, Sonarr, Radarr, Jellyseer and many
more. By default CasaOS does not offer that much when it comes to statistics
about the operating system. Ok, I get the RAM and CPU usage on each container
individually. On this hand is perfect, as I am a bit picky, I would want more
information about the temperature of the CPU, more about the GPU etc. I think
this will be a nice project for future me.

# Server Automation
When we talk about automation in servers, there is a plethora of options for
everyone's personal needs. Once you link a mail server, you just got yourself
a new toy to play.

# Security considerations

# Troubleshooting
