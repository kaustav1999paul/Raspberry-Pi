# Project Pi

<div align="center">
  <img src="/Raspberry_Pi_Logo.svg" alt="Raspberry Pi Logo" width="100">
</div>

# Raspberry Pi Monitoring Setup Using Netdata
This guide documents the setup of Netdata monitoring on the Raspberry Pi host machine.

Netdata provides:
- real-time monitoring
- CPU usage
- RAM usage
- disk usage
- network traffic
- Raspberry Pi temperature monitoring
- LXC container monitoring
- system load monitoring

The setup is lightweight and completely free.

---

# Architecture

```text
Ubuntu Host (Pi)
   │
   ├── Netdata Monitoring
   │
   ├── ai-instance
   ├── app-instance
   └── ad-instance
```

Netdata is installed directly on the HOST Pi instead of inside a container so that it can monitor:
- host machine
- all LXC containers
- hardware temperature
- overall infrastructure health

---

# Host Machine Details

## Hardware
- Raspberry Pi 4
- 4GB RAM

## Host OS

```bash
Ubuntu 26.04 LTS
```

---

# Install Netdata

Run on HOST Pi:

```bash
curl -fsSL https://get.netdata.cloud/kickstart.sh | sudo bash -s -- --non-interactive
```

This command:
- installs Netdata
- configures services
- enables monitoring
- starts dashboard automatically

---

# Verify Netdata Service

```bash
systemctl status netdata
```

Expected:

```text
active (running)
```

---

# Access Dashboard

Open in browser:

```text
http://192.168.0.123:19999
```

Replace with your actual Pi IP if different.

---

# What The Dashboard Shows

## System Metrics
- CPU usage
- RAM usage
- swap usage
- disk usage
- disk I/O
- network traffic
- processes

---

# Raspberry Pi Metrics
- CPU temperature
- system load
- uptime
- throttling indicators

---

# LXC Container Metrics
- ai-instance usage
- app-instance usage
- ad-instance usage
- container CPU usage
- container RAM usage
- container networking

---

# Useful Netdata Commands

## Check Service Status

```bash
systemctl status netdata
```

---

# Restart Netdata

```bash
sudo systemctl restart netdata
```

---

# Stop Netdata

```bash
sudo systemctl stop netdata
```

---

# Start Netdata

```bash
sudo systemctl start netdata
```

---

# Enable Auto Start

```bash
sudo systemctl enable netdata
```

---

# Disable Auto Start

```bash
sudo systemctl disable netdata
```

---

# View Netdata Logs

```bash
journalctl -u netdata -f
```

---

# Default Port

Netdata runs on:

```text
19999
```

Example:

```text
http://192.168.0.123:19999
```

---

# Security Recommendation

Currently the dashboard is:
- accessible only inside local network (LAN)

Recommended:
- do not expose publicly
- keep behind LAN/VPN only

---

# Resource Usage

Typical resource usage on Raspberry Pi 4:

| Resource | Usage |
|---|---|
| RAM | ~100-200MB |
| CPU | low |

Netdata is lightweight and suitable for 24×7 operation.

---

# Monitoring Benefits

The dashboard helps monitor:
- overheating
- memory pressure
- AI workload impact
- container resource usage
- disk usage growth
- network traffic
- overall infrastructure health

---
