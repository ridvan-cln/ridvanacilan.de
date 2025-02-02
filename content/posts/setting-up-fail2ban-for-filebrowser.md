+++
title = 'Setup Fail2Ban for File Browser'
date = 2024-03-28T19:04:20+01:00
draft = false
description = "A guide on setting up Fail2Ban for File Browser."
categories = ["Tutorials"]
tags = ["fail2ban", "filebrowser", "linux"]
+++

This guide describes how to setup fail2ban for [File Browser](https://filebrowser.org/).

### Install Fail2Ban

```sh
apt install fail2ban
```

### Configure Fail2Ban

A failed login attempt in the File Browser logs appears as:

```log
/api/login: 403 xxx.xxx.xxx.xxx <nil>
```

#### Create a Filter

Create the file `/etc/fail2ban/filter.d/filebrowser.conf` with the following content:

```ini
[Definition]
failregex = /api/login: 403 <ADDR> .*
ignoreregex =
journalmatch = _SYSTEMD_UNIT=filebrowser.service
```

#### Create a Jail Configuration

Create `/etc/fail2ban/jail.d/filebrowser.conf` and customize the actions as needed:

```ini
[filebrowser]
enabled = true
filter = filebrowser
action = cloudflare
         iptables-allports
backend = systemd
maxretry = 3
```

### Restart Fail2Ban

```sh
systemctl restart fail2ban.service
```

### Verify Setup

Run the following command to monitor Fail2Ban in real-time:

```sh
watch -n1 fail2ban-client status filebrowser
```

Then attempt one or more failed logins to ensure everything works as expected.
