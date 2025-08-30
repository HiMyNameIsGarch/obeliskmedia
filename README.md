# Obelisk Media
I have created this repository to document my process of making a home media
server using an old laptop via Jellyfin. The goal of this project is to create my own
hosted server.

# Table of Contents
- [Before installation requirements](#before-installation-requirements)
- [Installation process](#installation-process)
- [Post installation requirements](#post-installation-requirements)
- [CasaOS](#casaos)
- [Prometheus and Grafana](#prometheus-and-grafana)
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

# Prometheus and Grafana
If you read above about my problems when it comes to statistics, you can already
tell that I like fancy graphs. Here comes Prometheus and Grafana in hand. Prometheus
is one of many sources for Grafana and Prometheus cannot work without some data
sources, or exporters more explicitly. The first and basic exporter is `node_exporter`.

As in CasaOS you cannot "install" Node_exporter and Prometheus, you need to create
them manually. So I choose the following structure of containers via docker-compose.yml.

- exporters
    - node_exporter
    - qbittorrent_exporter
    - jellyfin_exporter
    - etc
- prometheus
- grafana ( which will be installed via CasaOS )

The containers are placed in `/var/lib/casaos/apps/`, run the following:
```
cd /var/lib/casaos/apps/

mkdir exporters promethus

# this creates the docker-compose files in both directories
touch {exporters,prometheus}/docker-compose.yml

# also create a scrape config for prometheus like:
touch prometheus/prometheus.yml
```

Now create the `node_exporter` container like so:
```
name: exporters
services:
    node-exporter:
        cpu_shares: 90
        command: []
        container_name: node_exporters
        deploy:
            resources:
                limits:
                    memory: "3767533568"
        hostname: node_exporters
        image: prom/node-exporter:latest
        network_mode: host
        restart: always
```

Then create the Prometheus container:
```
name: prometheus
services:
    prometheus:
        cpu_shares: 90
        command: []
        container_name: prometheus
        deploy:
            resources:
                limits:
                    memory: "3767533568"
        hostname: prometheus
        image: prom/prometheus:latest
        labels:
            icon: https://github.com/prometheus/docs/raw/ca2961b495c3e2a1e4586899c26de692fa5a28e7/static/prometheus_logo_orange_circle.svg
        network_mode: bridge
        ports:
            - mode: ingress
              target: 9090
              published: "9090"
              protocol: tcp
        restart: always - ./prometheus.yml:/etc/prometheus/prometheus.yml:ro
```

Now we need the scrape config like so:
```
global:
  scrape_interval: 10s

scrape_configs:
  - job_name: "node_exporter"
    static_configs:
      - targets: ["<your-local-ip>:9100"]

  - job_name: "<another-exporter"
    static_configs:
      - targets: ["<your-local-ip>:<your_port>"]
```
You can change the scrape time from 10 seconds to whatever you want and here
you will place all the scrapers.


# Server Automation
When we talk about automation in servers, there is a plethora of options for
everyone's personal needs. Once you link a mail server, you just got yourself
a new toy to play.

# Security considerations

# Troubleshooting
