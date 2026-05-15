# Raspberry Pi LXD Multi-Instance Infrastructure Setup Guide

## Host Machine Details

### Hardware
- Raspberry Pi 4
- 4GB RAM

### Host OS
```bash
Ubuntu 26.04 LTS
```

### Architecture Goal
Create isolated lightweight instances similar to cloud environments using LXC/LXD.

Instances created:
- `app-instance`
- `ai-instance`
- `ad-instance`

---

# Final Architecture

```text
Ubuntu Host (Pi)
│
├── app-instance
│   └── Spring Boot / APIs
│
├── ai-instance
│   └── Ollama / LLMs
│
└── ad-instance
    └── Experimental Workloads
```

---

# Why LXC/LXD Instead of Full VMs

LXC containers are:
- lightweight
- low memory overhead
- fast startup
- isolated environments
- suitable for Raspberry Pi

Unlike full virtual machines, LXC containers share the host Linux kernel.

---

# LXD Installation

## Install LXD

```bash
sudo snap install lxd
```

## Verify Installation

```bash
lxd --version
```

Expected:

```bash
5.21.4 LTS
```

---

# Initialize LXD

## Start Initialization

```bash
sudo lxd init
```

## Configuration Used

### Clustering
```text
no
```

### Configure new storage pool
```text
yes
```

### Storage backend
```text
dir
```

### Storage pool name
```text
default
```

### MAAS
```text
no
```

### Create network bridge
```text
yes
```

### Bridge name
```text
lxdbr0
```

### IPv4
```text
auto
```

### IPv6
```text
none
```

### LXD over network
```text
no
```

### Auto-update images
```text
yes
```

### YAML dump
```text
no
```

---

# Fix LXD Permission Issue

## Add current user to lxd group

```bash
sudo usermod -aG lxd pi
```

## Logout completely

```bash
exit
```

Reconnect to SSH afterwards.

---

# Verify LXD Setup

## List Networks

```bash
lxc network list
```

## List Storage Pools

```bash
lxc storage list
```

---

# Create Instances

## Create app-instance

```bash
lxc launch ubuntu:24.04 app-instance
```

## Create ai-instance

```bash
lxc launch ubuntu:24.04 ai-instance
```

## Create ad-instance

```bash
lxc launch ubuntu:24.04 ad-instance
```

---

# List All Instances

```bash
lxc list
```

Example Output:

```text
+--------------+---------+----------------------+------+-----------+-----------+
|     NAME     |  STATE  |         IPV4         | IPV6 |   TYPE    | SNAPSHOTS |
+--------------+---------+----------------------+------+-----------+-----------+
| ad-instance  | RUNNING | 10.224.67.30         |      | CONTAINER | 0         |
| ai-instance  | RUNNING | 10.224.67.179        |      | CONTAINER | 0         |
| app-instance | RUNNING | 10.224.67.91         |      | CONTAINER | 0         |
+--------------+---------+----------------------+------+-----------+-----------+
```

---

# Enter Individual Instances

## Enter app-instance

```bash
lxc exec app-instance -- bash
```

## Enter ai-instance

```bash
lxc exec ai-instance -- bash
```

## Enter ad-instance

```bash
lxc exec ad-instance -- bash
```

---

# Verify Current Instance

Inside container:

```bash
hostname
```

---

# Exit Current Instance

```bash
exit
```

---

# Start / Stop / Restart Instances

## Stop Instance

```bash
lxc stop ai-instance
```

## Start Instance

```bash
lxc start ai-instance
```

## Restart Instance

```bash
lxc restart ai-instance
```

---

# Delete Instance

## Force Delete

```bash
lxc delete ad-instance --force
```

---

# Container Information

## View Detailed Info

```bash
lxc info ai-instance
```

---

# Ollama Setup Inside ai-instance

## Enter ai-instance

```bash
lxc exec ai-instance -- bash
```

## Install Ollama

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

---

# Ollama Commands

## Run Model

```bash
ollama run qwen2.5:1.5b
```

## List Installed Models

```bash
ollama list
```

## Remove Model

```bash
ollama rm gemma3n:e2b
```

---

# Ollama Service Management

## Stop Ollama

```bash
systemctl stop ollama
```

## Start Ollama

```bash
systemctl start ollama
```

## Restart Ollama

```bash
systemctl restart ollama
```

## Check Ollama Status

```bash
systemctl status ollama
```

## Disable Auto Start

```bash
systemctl disable ollama
```

## Enable Auto Start

```bash
systemctl enable ollama
```

---

# Recommended Models For Raspberry Pi 4 (4GB)

## Recommended

```text
qwen2.5:1.5b
```

Other good options:
- tinyllama
- phi3:mini
- gemma:2b

---

# Important Notes

## Why gemma3n:e2b Failed

The model required:

```text
~5.8GB RAM
```

But Raspberry Pi 4 has:

```text
4GB total RAM
```

Therefore the model was too large for the hardware.

---

# Current Networking

LXD Bridge Network:

```text
lxdbr0
```

Container IPs:

| Instance | IP |
|---|---|
| app-instance | 10.224.67.91 |
| ai-instance | 10.224.67.179 |
| ad-instance | 10.224.67.30 |

---

# Planned Future Enhancements

- Spring Boot deployment in app-instance
- AI API integration between app-instance and ai-instance
- Docker inside containers
- Reverse proxy setup
- Snapshot backups
- ZRAM optimization
- Internal service communication
- Auto-start configuration
- User-based isolated login flows

---

# Useful Quick Commands

## List Containers

```bash
lxc list
```

## Enter Container

```bash
lxc exec <container-name> -- bash
```

## Stop Container

```bash
lxc stop <container-name>
```

## Start Container

```bash
lxc start <container-name>
```

## Restart Container

```bash
lxc restart <container-name>
```

## Delete Container

```bash
lxc delete <container-name> --force
```

## View Container Info

```bash
lxc info <container-name>
```

---

# Summary

Your Raspberry Pi is now configured as a lightweight multi-instance infrastructure platform using LXD/LXC.

This setup provides:
- isolated environments
- lightweight virtualization
- independent networking
- scalable architecture
- AI-ready infrastructure
- Spring Boot compatible architecture
- future extensibility

The setup is significantly more efficient for Raspberry Pi hardware compared to traditional hypervisors or full virtual machines.

