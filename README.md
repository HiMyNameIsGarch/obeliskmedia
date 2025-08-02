# Obelisk Media
I have created this repository to document my process of making a home media
server using an old laptop via Jellyfin. The goal of this project is to create my own
hosted server.

# Table of Contents
- [Before installation requirements](#before-installation-requirements)
- [Installation process](#installation-process)
- [Post installation requirements](#post-installation-requirements)
- [Security considerations](#security-considerations)
- [Troubleshooting](#troubleshooting)
- [Automated installation](#automated-installation)

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
- Install the latest updates for your operating system. Then you will observe
that jellyfin is not in the repositories. You will need to add it in the following way:

Start installing the neccesary packages:
```
sudo apt install apt-transport-https ca-certificates gnupg2 software-properties-common
```
Then import the Jellyfin GPG key:
```
curl -fsSL https://repo.jellyfin.org/jellyfin_team.gpg.key | sudo gpg --dearmor -o /usr/share/keyrings/jellyfin-archive-keyring.gpg
```
Now add the repository:
```
echo "deb [signed-by=/usr/share/keyrings/jellyfin-archive-keyring.gpg] https://repo.jellyfin.org/ubuntu $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/jellyfin.list
```
Finally, update the package list and install Jellyfin:
```
sudo apt update && sudo apt install jellyfin -y && sudo systemctl enable jellyfin
```
And voilà, you have installed Jellyfin on your server. You can now access it via
your web browser at `http://<your-server-ip>:8096`.

# Security considerations

# Troubleshooting

# Automated installation



