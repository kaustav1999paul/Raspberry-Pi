# Pi-hole Setup Guide Using LXC on Raspberry Pi

# Overview

This guide documents the complete setup of Pi-hole inside an LXC container (`ad-instance`) on a Raspberry Pi running Ubuntu Server with LXD.

The setup provides:
- isolated Pi-hole infrastructure
- network-wide ad blocking
- DNS filtering
- LAN-accessible Pi-hole container
- dedicated DHCP reservation
- scalable homelab architecture

---

# Infrastructure Architecture

```text
Ubuntu Host (Raspberry Pi)
│
├── ai-instance
│   └── Ollama / Local AI
│
├── app-instance
│   └── Spring Boot APIs
│
└── ad-instance
    └── Pi-hole DNS Server
```

---

# Host Machine Details

## Hardware
- Raspberry Pi 4
- 4GB RAM

## Host OS

```bash
Ubuntu 26.04 LTS
```

## LXD Version

```bash
5.21.4 LTS
```

---

# Why Pi-hole Was Installed In LXC

Using a dedicated LXC container provides:

- clean isolation
- independent networking
- safer DNS management
- easy backups/snapshots
- simplified maintenance
- no pollution of host OS

---

# Existing ad-instance Creation

## Create ad-instance

```bash
lxc launch ubuntu:24.04 ad-instance
```

---

# Enter ad-instance

```bash
lxc exec ad-instance -- bash
```

---

# Initial Package Preparation

## Update Packages

```bash
apt update && apt upgrade -y
```

## Install Utilities

```bash
apt install curl wget sudo nano net-tools -y
```

---

# Default LXC Network Problem

Initially the container used:

```text
10.224.x.x
```

through:

```text
lxdbr0
```

This internal NAT network is NOT ideal for Pi-hole because:
- router cannot use container directly as DNS
- devices cannot access container easily
- DNS server should exist directly on LAN

---

# Solution: macvlan Networking

A dedicated macvlan network was created so that the container receives its own LAN IP directly from the router.

---

# Create macvlan Network

Run on HOST Pi:

```bash
lxc network create lanbr0 --type=macvlan parent=eth0
```

---

# Override Existing Network Device

```bash
lxc config device override ad-instance eth0 network=lanbr0
```

---

# Restart Container

```bash
lxc restart ad-instance
```

---

# Verify New LAN IP

Enter container:

```bash
lxc exec ad-instance -- bash
```

Then:

```bash
ip a
```

Expected:

```text
192.168.0.x
```

Actual assigned IP:

```text
192.168.0.184
```

---

# Important macvlan Limitation

With macvlan:

```text
Host Pi ↔ ad-instance
```

cannot directly communicate.

However:

```text
Phones/Laptops/Router ↔ ad-instance
```

works perfectly.

This is acceptable and common for Pi-hole deployments.

---

# Install Pi-hole

Inside ad-instance:

```bash
curl -sSL https://install.pi-hole.net | bash
```

---

# Pi-hole Installer Configuration

## Static IP Warning

Continue installation.

The router reservation is configured later.

---

# Upstream DNS

Selected:

```text
Cloudflare (1.1.1.1)
```

---

# Block Lists

Used:

```text
Default block lists
```

---

# Web Admin Interface

Enabled:

```text
Yes
```

---

# Install Web Server

Enabled:

```text
Yes
```

---

# Query Logging

Enabled:

```text
Yes
```

---

# Privacy Mode

Selected:

```text
Privacy Level 0
```

Full query visibility enabled.

---

# Pi-hole Dashboard

Accessible at:

```text
http://192.168.0.184/admin
```

---

# Configure Router DNS

## TP-Link Archer C6

Navigate to:

```text
Advanced → Network → DHCP Server
```

---

# DNS Configuration Used

## Primary DNS

```text
192.168.0.184
```

## Secondary DNS

```text
0.0.0.0
```

Important:
- no Google DNS
- no Cloudflare DNS directly in router
- prevents DNS bypass

---

# DHCP Reservation

A permanent reservation was created.

## Reserved Device

| Device | Reserved IP |
|---|---|
| ad-instance | 192.168.0.184 |

---

# MAC Address Used

```text
00:16:3E:8E:1C:58
```

---

# Lease Time Recommendation

Current:

```text
120 minutes
```

Recommended:

```text
1440 minutes
```

(24 hours)

---

# Verify Pi-hole Is Working

## Open Dashboard

```text
http://192.168.0.184/admin
```

## Verify

- live DNS queries appear
- devices show up
- blocked domains increase
- ads disappear on clients

---

# Useful Pi-hole Commands

## Update Gravity Lists

```bash
pihole -g
```

---

# Restart DNS

```bash
pihole restartdns
```

---

# Disable Pi-hole Temporarily

```bash
pihole disable
```

---

# Re-enable Pi-hole

```bash
pihole enable
```

---

# Check Pi-hole Status

```bash
pihole status
```

---

# Tail Real-Time Logs

```bash
tail -f /var/log/pihole/pihole.log
```

---

# Update Pi-hole

```bash
pihole -up
```

---

# Useful LXC Commands

## List Containers

```bash
lxc list
```

---

# Enter ad-instance

```bash
lxc exec ad-instance -- bash
```

---

# Stop ad-instance

```bash
lxc stop ad-instance
```

---

# Start ad-instance

```bash
lxc start ad-instance
```

---

# Restart ad-instance

```bash
lxc restart ad-instance
```

---

# Delete ad-instance

```bash
lxc delete ad-instance --force
```

---

# Current Final Networking

| Component | IP |
|---|---|
| Host Pi | 192.168.0.123 |
| ad-instance | 192.168.0.184 |

---

# Final DNS Flow

```text
Devices
   ↓
Router DHCP
   ↓
Pi-hole (192.168.0.184)
   ↓
Cloudflare DNS
```

---

# Future Recommended Enhancements

## Better Blocklists
- OISD
- StevenBlack
- Firebog

---

# Local DNS Entries

Examples:

```text
ai.local
app.local
pi.local
```

---

# Regex Blocking

Advanced telemetry blocking.

---

# Backup Strategy

Export Pi-hole configuration periodically.

---

# Unbound Recursive DNS

Optional future enhancement for:
- full local DNS recursion
- increased privacy
- reduced third-party dependency

---

# Important Notes

## YouTube Ads

Pi-hole cannot reliably block YouTube ads because:
- ads and videos are served from same Google domains
- DNS-level blocking cannot distinguish streams reliably

However Pi-hole still helps reduce:
- telemetry
- trackers
- analytics

---

# Final Summary

The Raspberry Pi now runs:

- isolated AI infrastructure
- isolated Spring Boot environment
- isolated Pi-hole DNS server
- LAN-accessible network filtering
- centralized DNS control
- scalable homelab architecture

The Pi-hole deployment is production-style, lightweight, maintainable, and fully integrated into the local network using LXC and macvlan networking.

