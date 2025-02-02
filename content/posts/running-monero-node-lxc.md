+++
title = "Running a Monero Node in a Proxmox LXC"
date = 2024-03-28T00:00:00Z
description = "Guide to running a Monero node in a Proxmox LXC with minimal effort."
categories = ["Tutorials"]
tags = ["Monero", "Proxmox", "Docker", "LXC"]
+++

So you’ve decided to support the Monero network by running a node? Great! If you already have **Proxmox VE**, this guide will show you how to set it up with minimal effort.

### Prerequisite

Ensure that ports **18080** and **18089** are open. If you prefer to run your node through **Tor**, that is also an option but not covered in this guide.

### Install Docker in LXC

Run the Docker LXC installation script from the awesome [tteck collection](https://tteck.github.io/Proxmox/) in your VE console:

```sh
bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/ct/docker.sh)"
```

### Adjust Storage (If Needed)

If your SSD running PVE doesn't have enough space, create a new folder on an external HDD and mount it to the LXC:

```sh
pct set <LXC_ID> --mp0 HDD_PATH_ON_HOST/monero,mp=/root/monero
```

> **Note:** Running Monero on an HDD may result in slow performance. For the initial sync, consider using an **SSD** before transferring the data to an HDD.

### Run Monero Node

Thanks to the prework of [**sethforprivacy's** guide](https://sethforprivacy.com/guides/run-a-monero-node/), it is very easy to start the Monero node (adjusting the volume part as needed):

```sh
docker run -d --restart unless-stopped --name="monerod" \
    -p 18080:18080 -p 18089:18089 \
    -v /root/monero:/home/monero \
    ghcr.io/sethforprivacy/simple-monerod:latest \
    --rpc-restricted-bind-ip=0.0.0.0 --rpc-restricted-bind-port=18089 \
    --no-igd --no-zmq --enable-dns-blocklist
```

### Keep the Node Up-to-Date

Install **watchtower** to automatically update your Monero node when new versions are available:

```sh
docker run -d \
    --name watchtower --restart unless-stopped \
    -v /var/run/docker.sock:/var/run/docker.sock \
    containrrr/watchtower --cleanup \
    monerod
```

That’s it! Your Monero node is now up and running within a Proxmox LXC container. 🚀
